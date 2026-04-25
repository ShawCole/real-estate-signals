# Architecture Overview

**Phase 2 deliverable.** Reflects Shaw's scope decisions submitted via questionnaire `real-estate-signals-scope-v1` on 2026-04-25:

| Decision | Pick |
|---|---|
| Build model | Build ourselves — full IP ownership |
| MVP counties | **5**: Harris (TX), Cuyahoga (OH), Philadelphia (PA), Cook (IL), Maricopa (AZ) |
| Data sourcing | **No bulk licensing.** Raw county scrapers + BatchData ($39-119/mo) as the only paid layer for vacancy + skip-trace |
| Go-to-market | Single-seat first, white-label second |
| MVP signal scope | **Delinquency + vacancy only.** Code violations, probate, lis pendens, deed transfers are post-MVP expansions |
| Validation | Speculative but high-confidence — warm prospect pipeline |

---

## TL;DR

```
┌─────────────────────────────────────────────────────────────────┐
│  Sources                                                        │
│  ┌─────────────────────┐    ┌─────────────────────┐             │
│  │ County treasurers   │    │ BatchData API       │             │
│  │ (5 delinquency      │    │ ($39-119/mo)        │             │
│  │  scrapers, Python)  │    │ vacancy + skip-trace│             │
│  └──────────┬──────────┘    └──────────┬──────────┘             │
│             │                          │                        │
│             └────────────┬─────────────┘                        │
│                          ▼                                      │
│  ┌────────────────────────────────────────────────────────┐    │
│  │ Ingestion (Python)                                     │    │
│  │ fetch → normalize → dedupe → match-to-parcel → score   │    │
│  │ Cron-driven: delinquency weekly, vacancy daily         │    │
│  └────────────────────┬───────────────────────────────────┘    │
│                       ▼                                         │
│  ┌────────────────────────────────────────────────────────┐    │
│  │ PostgreSQL                                             │    │
│  │  parcels  ·  signals  ·  scores  ·  watchlists         │    │
│  └────────────────────┬───────────────────────────────────┘    │
│                       ▼                                         │
│  ┌────────────────────────────────────────────────────────┐    │
│  │ API (Node + Express)         Dashboard (React + Vite)  │    │
│  │ /properties /signals /scores  Map-first, TT layout      │    │
│  └────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

**Stack:** Python ingestion · PostgreSQL · Node/Express API · React/Vite dashboard · hosted on Shaw's VPS, Tailscale-served. Same shape as ListMagic_Dev to minimize new infrastructure.

**MVP timeline:** ~4 weeks for delinquency + vacancy across 5 counties. Code violations, probate, lis pendens added in subsequent 2-week cohorts.

---

## 1. Data model (PostgreSQL)

Property-centric, signals as 1-to-many child rows. Designed to extend cleanly when post-MVP signal types land.

```sql
-- Parcels: one row per property in our coverage
CREATE TABLE parcels (
  id              SERIAL PRIMARY KEY,
  parcel_id       TEXT NOT NULL,           -- county-assigned APN
  county_fips     TEXT NOT NULL,           -- e.g. '48201' for Harris TX
  state           TEXT NOT NULL,           -- 'TX', 'OH', etc.

  address_line    TEXT,
  city            TEXT,
  zip             TEXT,
  lat             DOUBLE PRECISION,
  lon             DOUBLE PRECISION,

  owner_name      TEXT,                    -- of record, may differ from skip-traced
  owner_mailing   TEXT,                    -- mailing address from county roll
  assessed_value  NUMERIC,
  market_value    NUMERIC,                 -- when published; many counties don't
  property_type   TEXT,                    -- SFR, condo, MF, vacant_land, commercial

  first_seen_at   TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  last_seen_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  raw             JSONB,                   -- preserve full county record for audit

  UNIQUE (county_fips, parcel_id)
);
CREATE INDEX idx_parcels_geo ON parcels (state, county_fips);
CREATE INDEX idx_parcels_owner ON parcels (owner_name);
CREATE INDEX idx_parcels_zip ON parcels (zip);

-- Signals: every distress event observed against a parcel
CREATE TABLE signals (
  id              SERIAL PRIMARY KEY,
  parcel_id       INTEGER NOT NULL REFERENCES parcels(id) ON DELETE CASCADE,

  signal_type     TEXT NOT NULL,           -- 'tax_delinquent', 'vacancy', 'code_violation', 'lis_pendens', 'probate', 'deed_transfer'
  source          TEXT NOT NULL,           -- 'harris_county_tax', 'batchdata', 'cuyahoga_clerk', etc.
  severity        INT NOT NULL,            -- 1..5; see scoring model below
  decay_tier      INT NOT NULL,            -- 1=days, 2=weeks, 3=months, 4=persistent

  observed_at     TIMESTAMPTZ NOT NULL,    -- when the event happened (filing date, etc.)
  ingested_at     TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  expires_at      TIMESTAMPTZ,             -- nullable; when this signal stops contributing

  payload         JSONB NOT NULL,          -- full source record for evidence
  source_url      TEXT,                    -- deep-link to source doc when available

  UNIQUE (parcel_id, signal_type, source, observed_at)
);
CREATE INDEX idx_signals_parcel ON signals (parcel_id);
CREATE INDEX idx_signals_type_observed ON signals (signal_type, observed_at DESC);

-- Scores: computed nightly per parcel
CREATE TABLE scores (
  parcel_id       INTEGER PRIMARY KEY REFERENCES parcels(id) ON DELETE CASCADE,

  composite       INT NOT NULL,            -- 0..100
  signal_count    INT NOT NULL,            -- how many active signals (post-decay)
  components      JSONB NOT NULL,          -- {"tax_delinquent": 25, "vacancy": 35, ...}

  computed_at     TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_scores_composite ON scores (composite DESC);

-- Watchlists: user-saved property collections
CREATE TABLE watchlists (
  id              SERIAL PRIMARY KEY,
  user_id         TEXT NOT NULL,
  name            TEXT NOT NULL,
  filter          JSONB NOT NULL,          -- saved filter spec (county, score_min, signal_types, etc.)
  notify          BOOLEAN NOT NULL DEFAULT FALSE,
  created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Owner enrichment cache (skip-trace results from BatchData)
CREATE TABLE owner_contacts (
  parcel_id       INTEGER PRIMARY KEY REFERENCES parcels(id) ON DELETE CASCADE,
  phones          TEXT[],
  emails          TEXT[],
  mailing_address TEXT,
  source          TEXT NOT NULL,           -- 'batchdata'
  matched_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  raw             JSONB
);
```

Why JSONB `raw` and `payload` columns: every county schema is different. Storing the source record verbatim means we never lose data when a portal changes — we can re-derive normalized columns later without re-scraping.

---

## 2. Scraper plugin interface

Every county scraper is a Python module with this signature:

```python
# scrapers/base.py
from dataclasses import dataclass
from typing import Iterator
from datetime import datetime

@dataclass
class RawSignal:
    parcel_id: str           # county APN
    county_fips: str
    signal_type: str         # 'tax_delinquent', etc.
    severity: int            # 1..5
    decay_tier: int          # 1..4
    observed_at: datetime
    payload: dict            # full record
    source_url: str | None = None
    # optional parcel-enrichment fields (when scraper also pulls parcel data)
    address_line: str | None = None
    city: str | None = None
    zip: str | None = None
    owner_name: str | None = None
    owner_mailing: str | None = None
    assessed_value: float | None = None

class Scraper:
    name: str                # 'harris_county_tax_delinquent'
    county_fips: str         # '48201'
    state: str               # 'TX'
    cadence: str             # 'weekly', 'daily', 'monthly'

    def fetch(self) -> Iterator[RawSignal]:
        """Yield RawSignal records. Implementations handle auth, pagination, retries."""
        raise NotImplementedError
```

Driver loop (`scripts/run_scrapers.py`) walks `scrapers/`, imports any class subclassing `Scraper`, and runs the cohort that matches the cadence schedule. Each scraper's output stream goes through `normalize → dedupe → upsert_parcel → insert_signal`.

### MVP scraper inventory

For delinquency (5 county-specific scrapers — different portal each):

| File | Target | Portal pattern |
|---|---|---|
| `scrapers/harris_tx_tax.py` | Harris County TX | HCAD assessor + Tax Office (tax-master.org) |
| `scrapers/cuyahoga_oh_tax.py` | Cuyahoga County OH | MyPlace + Treasurer (cuyahogacounty.us) |
| `scrapers/philadelphia_pa_tax.py` | Philadelphia PA | Atlas / OPA Property + Revenue Dept |
| `scrapers/cook_il_tax.py` | Cook County IL | Treasurer (cookcountytreasurer.com) |
| `scrapers/maricopa_az_tax.py` | Maricopa County AZ | Assessor + Treasurer (mcassessor.maricopa.gov) |

For vacancy: 1 single integration covering all 5 counties (BatchData property-search API with vacancy filter).

For skip-trace (Phase 2.5, when we want owner contact info): same BatchData endpoint adds owner phones/emails.

### Vendor-pattern scrapers (post-MVP, for fast county expansion)

The 5 county scrapers above are written first as one-offs, but as we pattern-match, they collapse into reusable adapters. Per `research/03-state-feasibility.md`:

| Adapter | Powers |
|---|---|
| `scrapers/_patterns/qpublic.py` | qPublic (Schneider) — most of GA, parts of NC/FL/IN |
| `scrapers/_patterns/tyler_publicsearch.py` | Tyler Tech — many TX/OH/MI counties |
| `scrapers/_patterns/accela.py` | Code violations — most major cities |
| `scrapers/_patterns/fidlar.py` | Fidlar DirectSearch — Indiana + parts of IL/IA |
| `scrapers/_patterns/arcgis_rest.py` | Generic ArcGIS Feature Service — universal escape hatch |

Each county becomes a 20-line YAML config in `scrapers/_configs/{county_fips}.yaml` that wires the right adapter to the right URL/params. **5 adapters + 100 configs = 100 counties supported.** This is the architectural payoff for picking "no licensing" — we get scale through pattern reuse, not per-county rewriting.

---

## 3. Scoring model

Mirrors Andy's signal-stacking approach (`research/00-thomas-taylor-reverse-engineering.md`) but simplified for the 2-signal MVP.

### Decay tiers (severity multiplier × decay function)

| Tier | Decay | MVP signals in this tier |
|---|---|---|
| 1 — Act Now | Days | Future: NOTS, eviction final judgment, fire/condemnation order |
| 2 — 30-90 Days | Weeks | Vacancy (BatchData) — fresh USPS-NCOA flag, decays after 90d |
| 3 — Nurture | Months | Tax delinquent year 1, single code violation |
| 4 — Context | Persistent | Tax delinquent multi-year, owner age 65+, free-and-clear |

### Severity → base points (configurable; ships in `scoring/weights.yaml`)

```yaml
tax_delinquent:
  year_1:        25   # tier 3
  year_2_plus:   45   # tier 4 (escalates over years)
vacancy:
  fresh:         35   # tier 2 — newly flagged in last 90d
  stale:         20   # tier 4 — flagged > 90d ago
```

### Composite formula

```
composite = min(100, sum(active_signal_points))
signal_count = count(signals where expires_at IS NULL OR expires_at > now())
```

### MVP score interpretation

| Composite | Label |
|---|---|
| 0-19 | cold |
| 20-39 | cool |
| 40-59 | watch |
| 60-79 | warm |
| 80-100 | **hot** |

A property with both tax delinquent year-2+ (45) and fresh vacancy (35) lands at 80 — automatic hot. A property with only one signal lands in watch/warm. This matches Andy's "1 signal = noise, 2 = watch, 3+ = hot" heuristic, scaled for 2-signal MVP.

When we add code violations / probate / lis pendens in post-MVP cohorts, weights extend the same YAML — no code change.

---

## 4. Refresh cadence

Cron jobs run on the VPS, scheduled by `cron` and observed via journald.

| Job | Cadence | Owner |
|---|---|---|
| County tax delinquency scrapers | Weekly (Sun 02:00 UTC) | each county scraper |
| BatchData vacancy refresh | Daily (03:00 UTC) | `batchdata_vacancy.py` |
| Score recomputation | Nightly (04:00 UTC, after scrapers) | `scripts/recompute_scores.py` |
| Watchlist alerts | Hourly (when notify=true watchlists exist) | `scripts/notify_watchlists.py` |

### Observability requirements (MVP must-have)

- Each scraper run logs to `logs/scraper_{name}_{date}.log` with: rows fetched, rows new, rows changed, duration, errors.
- A heartbeat row in a `scraper_runs` table (omitted from schema above for brevity but spec'd in `02-data-pipeline.md`) tracks the last-success timestamp per scraper.
- Slack/Telegram alert if any scraper hasn't succeeded in 2× its cadence (e.g. weekly scraper with no success in 14 days = alert).

This is the difference between "we have 50 county scrapers" and "we have 50 working county scrapers." The TT platform doesn't appear to have any of this and that's why they had 3 outages.

---

## 5. API surface (Node + Express)

Mounted at `/api/re-signals/*` (under the existing OrchestraOS API on port 8888) so we don't run a second server. Routes:

| Method | Path | Returns |
|---|---|---|
| GET | `/properties` | Filter by `state`, `county`, `score_min`, `signal_types[]`, `bbox`. Returns paginated parcels with composite score. |
| GET | `/properties/:id` | Full property card: parcel + all signals + score components + owner contacts (when present). |
| GET | `/properties/:id/signals` | Signals only, time-ordered. |
| GET | `/heatmap` | Aggregated score density by H3 cell or zip — for the map view. |
| GET | `/watchlists` / `POST` `/watchlists` | User watchlist CRUD. |
| POST | `/properties/:id/skiptrace` | On-demand skip-trace via BatchData (rate-limited, costs $$). |
| GET | `/export.csv?filter=...` | CSV export of filtered properties. |

All routes require an `Authorization: Bearer <api_key>` header. MVP auth is a static API key per user; OAuth deferred.

---

## 6. Dashboard (React + Vite)

Six pages mirroring TT's layout, but **map-first** and **property-centric** (per `research/00` §"What we'd build differently"):

| # | Page | Content |
|---|---|---|
| 01 | **Overview** | Hero stats: total parcels indexed, hot count, signals-this-week, recent additions. Tile grid linking to other pages. |
| 02 | **Map** | The default landing view. Heat map by H3-cell score density; click to zoom; filter sidebar (county, score, signal types, price range). |
| 03 | **Property Feed** | Chronological list of newly-hot properties (rising-score events from the last 7 days). |
| 04 | **Property Card** | Full detail: address, owner, photos (Google Street View embed), all signals with timeline, score components, skip-trace owner contacts, outbound draft (cold-call script + postcard copy). |
| 05 | **Watchlists** | Saved filters with score-change alerts. |
| 06 | **Methodology** | Public-facing source-of-truth doc. Same role as TT's Methodology page — credibility-building for prospects. |

Visual language: **identical to TT** — dark navy gradient, mono numerals, kicker labels in mono caps, single accent color (we'll pick something other than TT red — probably a green or amber to differentiate). This isn't laziness; it's that TT's design language is genuinely good for dense-data dashboards and we'd reinvent the wheel to do anything else.

---

## 7. Stack & deploy

| Layer | Tech | Why |
|---|---|---|
| Ingestion | Python 3.11 + Playwright (for portals that need JS) + httpx (for APIs) + pydantic (for schema validation) | Best ecosystem for scraping; Andy himself listed BeautifulSoup/spaCy/newspaper3k as the case for Python |
| Storage | PostgreSQL 16 (existing instance on Shaw's VPS — already used by ArkData per `feedback_arkdata_postgres_only.md`) | One DB to rule them all; uses existing backup/monitoring |
| API | Node 20 + Express + TypeScript (mount under existing OrchestraOS API) | Consistent with ListMagic_Dev and the rest of OrchestraOS; no new server to babysit |
| Dashboard | React 18 + Vite + Tailwind + maplibre-gl | Vite for speed; maplibre-gl is the open-source mapbox replacement (no per-load fees) |
| Hosting | Shaw's VPS (srv1397016), Tailscale-served (`https://srv1397016.tail8be541.ts.net:PORT`) | No new infra; no Netlify rebuild fragility (per `research/00` lesson learned from Andy) |
| Scheduling | systemd timers (preferred over cron for journald logging + drop-in observability) | Cleaner failure surface than cron |

### Repo layout (target end-of-MVP)

```
real-estate-signals/
├── api/                  # Node/Express routes mounted under OrchestraOS API
│   ├── routes/
│   │   ├── properties.ts
│   │   ├── signals.ts
│   │   ├── heatmap.ts
│   │   └── watchlists.ts
│   └── server.ts         # only when run standalone; otherwise registered into agent-orchestra/api
├── dashboard/            # React/Vite frontend
│   ├── src/
│   │   ├── pages/        # Overview, Map, PropertyFeed, PropertyCard, Watchlists, Methodology
│   │   ├── components/
│   │   └── lib/api.ts
│   └── package.json
├── ingestion/            # Python pipeline
│   ├── scrapers/
│   │   ├── base.py
│   │   ├── harris_tx_tax.py
│   │   ├── cuyahoga_oh_tax.py
│   │   ├── philadelphia_pa_tax.py
│   │   ├── cook_il_tax.py
│   │   ├── maricopa_az_tax.py
│   │   ├── batchdata_vacancy.py
│   │   └── _patterns/    # qpublic, tyler_publicsearch, accela, fidlar, arcgis_rest
│   ├── normalize.py
│   ├── score.py
│   └── pyproject.toml
├── db/                   # SQL migrations
│   └── migrations/
│       ├── 001_init.sql
│       └── 002_*.sql
├── deploy/
│   ├── systemd/          # *.service + *.timer units
│   └── scripts/
└── research/             # (existing) Phase 1 docs
```

---

## 8. MVP timeline (4 weeks)

| Week | Deliverable | Definition-of-done |
|---|---|---|
| **1** | Schema + first scraper + BatchData wired | `db/migrations/001_init.sql` applied; Harris-county delinquency scraper writes parcels + signals to Postgres; BatchData test query returns vacancy data. |
| **2** | All 5 county delinquency scrapers + nightly score job | Cuyahoga, Philly, Cook, Maricopa scrapers shipping; `recompute_scores.py` cron writes to `scores`; observability (journald + heartbeat table) live. |
| **3** | API + dashboard skeleton (Overview + Map) | Node routes for `/properties`, `/heatmap` returning real data; React dashboard shows map with hot properties pinned across 5 counties. |
| **4** | Property Card + Watchlists + soft-launch polish | Click-through from map to property card with full signal stack + skip-trace + outbound draft; watchlist save/notify works; deploy via Tailscale; soft-launch to Shaw's warm prospect. |

**Definition of MVP done:** the warm prospect logs in, sees a map of 5 counties heat-mapped by distress score, clicks a hot Cleveland property, sees `tax_delinquent year 2+ (Cuyahoga Treasurer, observed 2026-03-15)` AND `vacancy (BatchData, observed 2026-04-22)` stacked into a composite score of 80, gets the owner's phone number via skip-trace, and exports the lead to CSV.

---

## 9. Post-MVP expansion roadmap

Each cohort is ~2 weeks. Done in order; each adds value to all 5 counties simultaneously.

| Cohort | Adds | Why this order |
|---|---|---|
| 5 | **Code violations** for the 5 MVP counties | Highest-converting distress signal; 5-10× wholesale conversion vs tax-delinquent. The wedge from `research/02`. Cleveland + Cook + Philly already have city open-data feeds. |
| 6 | **Lis pendens / NOD / NTS** for the 5 counties | Pre-foreclosure 30-120 day window; 5 county clerk scrapers using Tyler PublicSearch + qPublic adapters. |
| 7 | **Probate filings** for the 5 counties | Needs separate scrapers per probate court; medium difficulty; high conversion when present. |
| 8 | **Deed transfers** ($1, quitclaim, deed-in-lieu) | Recorder bulk pulls; adds pre-distress and post-distress context. |
| 9-12 | **Expansion to 20 counties** using the now-built vendor-pattern adapters | Each new county is a YAML config + smoke test, not a new scraper. ~2 counties/week. |

Total path to 20 counties × all 6 signal types ≈ 6 months from MVP start.

---

## 10. Cost shape (revised from `research/04-synthesis.md` for no-licensing scope)

### Build (one-time, weeks 1-4 + cohorts 5-8 = 12 weeks total)

| Item | Estimate |
|---|---|
| Engineering (1 dev × 12 wks) | $40-90K |
| Design / dashboard polish | $5-10K |
| Misc infra setup | $1-3K |
| **Total build** | **$46-103K** |

(Down from original $60-140K because no licensing setup costs.)

### Run (year 1, post-launch)

| Item | Estimate |
|---|---|
| BatchData (vacancy + skip-trace, ~50K records/mo at $39-119/mo plus per-call) | $1-5K/yr |
| Hosting (Shaw's VPS, no new infra) | $0 incremental |
| Postgres (existing) | $0 incremental |
| Engineering maintenance (0.25 FTE) | $25-50K/yr |
| **Total run** | **$26-55K/yr** |

(Down from original $220-385K/yr — the no-licensing decision saves ~$180-330K/yr.)

**Breakeven:** at $99/mo single-seat, breakeven is **22-46 paying users**. At $1.5K/mo white-label-to-brokerage tier (lower than original $5K assumption since we're not bundling enterprise data), breakeven is **2-3 contracts**. The warm-prospect pipeline only needs to convert one to recoup year 1.

---

## 11. Open architectural decisions (need Shaw's input before week 1 starts)

These are smaller than the Phase 1 questions but still need a yes/no:

1. **Mount under OrchestraOS API or run a standalone service?** Recommend mount — uses existing auth, monitoring, Tailscale routes. But it couples release cycles. Alternative: standalone Node service on a different port (`8889` is free).
2. **Single-tenant or multi-tenant from day one?** Single-tenant ships faster (just Shaw + warm prospect). Multi-tenant means we can sell single-seat without a rebuild later. Recommend **multi-tenant from day one** — `tenant_id` on every row is cheap to add now, expensive to retrofit.
3. **BatchData contract type.** They have monthly + per-call pricing. Recommend monthly entry tier ($39/mo) until volume justifies pro tier ($119/mo). Ramps with usage; no commitment.
4. **Map provider.** Maplibre + OpenStreetMap tiles is free and self-host-able. Mapbox tiles are prettier but $0.50 per 1K loads. Recommend Maplibre + OSM for MVP; switch later if needed.
5. **Domain.** Need a public URL (re-signals.io? distresslab.com? something else)? Or keep it internal at the Tailscale URL until the warm prospect signs?

If you want to flip the questionnaire form again I can spin up `architecture-decisions-v1` — otherwise, I'll proceed with the recommended defaults and you can override anything in week 1.
