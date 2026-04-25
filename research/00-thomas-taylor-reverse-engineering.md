# Reverse-Engineering thomas-taylor-signals.netlify.app

**Source platform:** https://thomas-taylor-signals.netlify.app/ (password: `thomas-taylor2026`)
**Built by:** Andrew "Andy" Shwetzer (Shaw's invisible build partner)
**White-labeled to:** Thomas & Taylor Search Partners (TTSP) + Thomas & Taylor Partners Holdings (TTPH)
**Date analyzed:** 2026-04-24

This document reverse-engineers the live thomas-taylor signal platform so we can **translate the exact same pattern to distressed real estate**. The platform exists, it works, and the architecture is documented in Andy's own messages to Shaw — we don't need source code access to know what it does or how to rebuild it.

---

## What the platform actually is

TT Signal Intelligence is a **6-page dark-themed dashboard** that converges five layers of public data into a single operational feed, then splits the same feed two ways for two different revenue motions. It is neither a CRM nor an outbound tool — it is a **research surface** that makes hidden buying / hiring / acquisition signals visible against a defined target universe.

### The 6 pages

| # | Page | Purpose |
|---|------|---------|
| 01 | **Overview** | Hero stats (DoD $ obligated, SEC filings, regulatory filings, named execs indexed). Tile-grid index of the other 5 pages. |
| 02 | **Signal Platform** | Chronological feed of every event from the last 180 days, badged by signal type (contract / filing / exec_change / ma / reg / insider / bill / proxy). Each row deep-links to the underlying source document. |
| 03 | **Search & M&A Signals** | Same data split into two motion-specific lenses. Search lens reads a CFO departure as "search mandate opening." M&A lens reads the same departure as "acquisition opening — successor not lined up." |
| 04 | **Candidate & Target Universe** | Named officer roster across 8 monitored primes (~98 execs), each linked to a Google search URL that pre-fills `name + company + linkedin`. Same companies are listed as M&A targets on the Holdings tab. |
| 05 | **Methodology** | The architecture — 5 data layers, refresh cadences, worked examples (one raw record + derivation logic per layer). This is the credibility-building page they show to prospects. |
| 06 | **Outbound Drafts** | LinkedIn post drafts grouped by motion, hooked off the latest signals. Manual-send, not auto-publish. |

### The 5 data source layers (Andy's stack, verbatim from the Methodology page)

| Layer | Source | What it surfaces | Refresh |
|---|---|---|---|
| 01 | USAspending.gov `/spending_by_award` | Every DoD contract over $1M, by recipient, NAICS, awarding agency | Daily |
| 02 | SEC EDGAR | 8-K (material events), DEF 14A (proxy / named officers), Form 4 (insider transactions) | Daily (4 business days post-event) |
| 03 | FederalRegister.gov | DoD, DHS, State, Commerce, BIS rule changes (ITAR, CFIUS, CMMC, export controls) | Daily |
| 04 | GovTrack / Congress.gov | NDAA, appropriations, defense authorization bills | Weekly |
| 05 | Company leadership pages (Lockheed, Boeing, RTX, Raytheon, Northrop, GD, L3Harris, Leidos) | Named officer ground truth — re-scraped on refresh | Monthly, or on-trigger by an 8-K Item 5.02 |

### Visual / brand language

- Dark navy gradient (`#0a0e17` → `#111827`)
- Single accent — TTSP red (`#EE3828` → `#FF5A4D`)
- JetBrains Mono for numbers / source pills, Inter for body, Source Sans Pro for headers
- Live indicator: pulsing green dot + "Live data · {date}"
- Color-coded signal badges (8 distinct categories)
- Sticky double-row header (logo + nav)
- `src-pill` mono link chips on every record citing the underlying public source
- Tile cards with hover lift, no shadows, `1px` rgba borders

The aesthetic is "Bloomberg Terminal meets minimalist VC dashboard." It signals authority and currency without feeling like a SaaS product.

---

## What's actually under the hood (Andy's own architecture spec)

Andy laid this out to Shaw in a WhatsApp message on **2026-04-02 at 4:29 AM** (preserved in `state/clients/thomas-taylor/analysis/Andy_Full_Convo.txt` lines 3883-4040). It is the closest thing we have to a source-of-truth blueprint.

### Signal-decay tier model

Andy's framework — adapted from PredictLeads / ColdIQ for the A&D vertical — places every signal into one of four decay tiers:

| Tier | Decay | Search-motion examples | Holdings-motion examples |
|---|---|---|---|
| 1 — Act Now | Days | Leadership departure (8-K), VP+ job posted, major contract award | Founder retirement announced, M&A advisor engaged, PE fund in final year |
| 2 — 30-90 Days | Weeks | Hiring surge (5+ roles), PE acquisition (needs mgmt team), new division launched, contract ramp-up | Contract expiration cluster, revenue plateau, competitor acquired, no succession visible |
| 3 — Nurture | Months | 1-3 senior job posts, award/recognition, product launch | Founder 60+, CMMC compliance pressure, getting house in order |
| 4 — Context | Persistent | News mentions, conference attendance, social activity, defense budget shifts | Industry consolidation trends, subsector M&A wave, patent portfolio strength |

### Signal stacking → account score

> "One signal = noise. Two aligned signals = watch. Three+ = hot account."
>
> *Example (Holdings):* Granite State Manufacturing — Founder is 67 (Tier 3, +15) + $40M revenue with 80% from one Army contract expiring in 14 months (Tier 2, +25) + CFO connected with 3 M&A attorneys on LinkedIn (Tier 1, +35) + just achieved CMMC Level 2 (Tier 3, +10) = **Score: 85/100 — Hot Account**.

Each signal carries a tier-weighted point value. Multiple aligned signals on the same entity stack into a 0-100 composite. The dashboard surfaces accounts by score, not by recency.

### The actual data source list Andy proposed (10 free APIs + 8 scrape targets)

**Free APIs (build first):**
1. SEC EDGAR (10 req/sec) — 8-K, Form 4, proxy, real-time
2. USASpending.gov (no key) — contract awards, daily
3. SAM.gov (10K req/day) — entity registrations, exclusions, real-time
4. Defense.gov RSS — daily contract award announcements (5pm ET)
5. SBIR.gov — defense tech startups Phase I→III, quarterly
6. PatentsView (45 req/min) — patent portfolios, IP transfers, weekly
7. Federal Register (1K req/min) — DFARS, acquisition reg, daily
8. Google News RSS — aggregated news, real-time
9. NewsAPI.org (free 100/day) — structured news search
10. Congress.gov — NDAA / budget, as-filed

**Scrape targets (build second):**
1. Defense News / SpaceNews / C4ISRNET — RSS
2. PR Newswire / Business Wire — RSS by industry
3. Company career pages (Workday JSON endpoints, top 20 primes use Workday)
4. DSCA FMS notifications — HTML scrape
5. CMMC Marketplace — CyberAB directory
6. GAO / DoD OIG reports — RSS
7. WARN Act filings — state DOL sites
8. State UCC filings — state SOS sites

**1st-party (ArkData pixel — highest value):**
- Visited TTSP / TTPH site (high)
- Repeat visit in 7 days (very high)
- Viewed specific service pages (very high)
- Form / Calendly submission (strongest)

### Stack options Andy floated

Andy gave Shaw three architectural choices:
1. **Node.js backend + cron** (consistent with ListMagic_Dev / Express stack), PostgreSQL store
2. **Python ingestion** (BeautifulSoup, spaCy, newspaper3k) + Node.js frontend
3. **Agent-based** — dispatch signal-collection agents via Agent Orchestra, run overnight, push to dashboard

We don't know which Andy ultimately shipped — the deployed Netlify site appears to be a **statically-generated HTML output** (we have v1-pages and v2-pages snapshots, all pure HTML/CSS, no client-side JS visible in the captured pages). Most likely the pipeline is: cron (somewhere — VPS, Render, or Railway) → fetch APIs → score → render Jinja/Handlebars templates → deploy static HTML to Netlify. The "Live data · 2026-04-22" date in the header is hardcoded per build.

### Hosting fragility (relevant context)

Per Shaw's notes in `state/clients/thomas-taylor/client.json`:

> V1/V2 on Netlify — went down 3 times (Apr 3, Apr 8, Apr 17-18). Andy promised immutable hosting migration; not yet delivered.

This is a structural reason for Shaw to want to own the rebuild rather than depend on Andy.

---

## Translating the pattern to distressed real estate

The thomas-taylor pattern is **not domain-specific** — it is a generic "converge N public data sources, score entities, surface hot ones" template. Real estate slots in cleanly:

### TTSP → Real Estate equivalent mapping

| TT concept | Real estate equivalent |
|---|---|
| **Entity** = defense prime (Lockheed, Boeing, …) | **Entity** = parcel / property (parcel ID + address) |
| **Source layer 01** = USAspending contract awards | **Source layer 01** = County tax delinquency rolls |
| **Source layer 02** = SEC EDGAR 8-K filings | **Source layer 02** = Foreclosure filings (lis pendens, NOD, NOTS) at county clerk |
| **Source layer 03** = Federal Register (regulatory pressure) | **Source layer 03** = City code violations / condemnation / demolition orders |
| **Source layer 04** = Congress.gov bills (legislative pressure) | **Source layer 04** = Probate filings + court records (divorce, bankruptcy) |
| **Source layer 05** = Company leadership pages (entity ground truth) | **Source layer 05** = County assessor parcel rolls (entity ground truth — owner, address, assessed value) |
| **Two motions** (Search vs M&A) | **Two motions** (Wholesale-flip vs Buy-and-hold rental) — same property scored differently for each |
| **Account score** (0-100, signal stacking) | **Distress score** (0-100, signal stacking) |
| **Signal decay tiers** (days / weeks / months / persistent) | Same model — eviction filing decays in days, code violation in months, owner-age-65+ is persistent context |
| **Outbound Drafts** (LinkedIn posts) | **Outreach drafts** (skip-traced phone scripts, postcard copy, cold-call openers) |

### Real-estate signal decay tiers (proposed)

| Tier | Decay | Buy-and-hold (rental) lens | Wholesale-flip lens |
|---|---|---|---|
| 1 — Act Now | Days | NOTS published (auction in 21-90 days), eviction final judgment, fire/condemnation order | Same — plus newly-listed-then-withdrawn, "as-is cash only" listing posted |
| 2 — 30-90 Days | Weeks | Lis pendens filed, NOD recorded, repeat code violation (3+ months), utility shutoff confirmed | Same — plus quit-claim deed for $1, multi-year tax delinquency |
| 3 — Nurture | Months | Tax delinquent (year 1), single code violation, MLS expired/withdrawn, owner-occupied flag flipped to absentee | Same — plus probate filing on owner-of-record |
| 4 — Context | Persistent | Owner age 65+, out-of-state owner, free-and-clear (no mortgage), high equity (>50%) | Same |

### Hot-property scoring example (mirrors Andy's "Granite State" example)

**Property: 1234 Elm St, Cuyahoga County, OH**
- Tax delinquent 2 consecutive years (Tier 3, +15)
- 4 code violations in last 6 months — overgrown, broken windows, junk vehicle (Tier 2, +20)
- Owner of record died Feb 2026, probate filed (Tier 1, +30)
- Out-of-state heir address in TX (Tier 4, +10)
- USPS marked vacant 90 days (Tier 2, +15)
- = **Score: 90/100 — Hot Property** (likely heirs unloading at discount; act before MLS)

---

## What we'd build differently from Andy's version

1. **No reliance on Netlify static rebuilds.** We'd serve a real backend (Node or Python on VPS / Render / Railway) so the dashboard updates the moment a scraper finishes, not on next build. Removes Andy's "went down 3 times" failure mode.
2. **Per-county scraper plugins** instead of monolithic ingestion. Each county is a Python module implementing a standard interface (`fetch() → list[Signal]`). New county = new file, no core changes. This is the architectural difference between "we covered 13 defense primes" (closed universe, easy) and "we covered Harris County TX" (one of 3,000+ US counties — must be infinitely extensible).
3. **Property-centric data model**, not company-centric. One canonical `parcel` row per property, signals attached as a 1-to-many child table with `(signal_type, source, observed_at, severity, raw_payload)`. Matches the actual unit of analysis investors care about.
4. **Signal freshness display** — every record shows "first seen X days ago" so users understand decay. TT's current dashboard buries this.
5. **Map-first UI**, not list-first. Real estate is geospatial. Heat-map by score density, click to drill into the property card. TT's list-first design works for 98 execs across 8 companies; doesn't scale to millions of parcels.
6. **Owner skip-trace as a built-in step**, not an afterthought. The TT product points users at `google.com/search?q={name}+linkedin`; the real-estate equivalent is a paid skip-trace API call (BatchSkipTracing $0.10-0.20/lookup, TLOxp ~$0.50-1.00) that resolves the owner's mailing address, phone, and email to a contact record on the property page.

---

## Open questions for Shaw before we commit to architecture

1. **Build vs partner with Andy?** Andy already has the pattern working for one vertical. Worth asking him to do a real-estate fork on revenue share before we rebuild from scratch — or is the goal explicitly to own the IP this time given the white-label hosting fragility?
2. **National coverage from day one, or 3-5 MVP counties?** True nationwide = 3,000+ counties = 12-24 months of scraper-writing. MVP = Harris (TX) + Cuyahoga (OH) + Maricopa (AZ) + Cook (IL) ships in 6-8 weeks and proves the model.
3. **Bulk-data shortcut: realestateapi.com / ATTOM / DataTree.** Andy himself pointed at realestateapi.com on 2025-04-23 (Andy_Full_Convo line 278). For ~$0.05-0.50 per record, we can buy normalized bulk parcel + foreclosure + tax data and skip the per-county scraper marathon entirely for the data-acquisition layer — then layer our own scrapers on top only for sources the bulk vendors don't cover (city code violations are the big gap).
4. **Who's the buyer?** Single-seat investor at $99-299/mo (PropStream tier) or white-labeled to a brokerage / wholesaler network at $5-15K/mo (the TTSP model)?
5. **Does this client exist already, or are we building speculatively?** The TTSP build was paid for. A speculative real-estate version is a different commitment.

These five answers determine the scope. Recommend a 30-min call before Phase 2 architecture starts.

---

## What this means for our research scope

The other research docs in this directory cover the universe-mapping work that needs to happen regardless of which path Shaw chooses:

- `01-data-sources.md` — full catalog of public real estate data sources (counterpart to Andy's "10 free APIs + 8 scrape targets" — but for property data)
- `02-existing-platforms.md` — competitive analysis of PropStream, BatchLeads, DealMachine, etc. (the equivalent of TT's competitive landscape)
- `03-state-feasibility.md` — county-by-county scraper feasibility for the top 10 investor states (the operational scope question)

When all four docs land, the synthesis becomes a one-page "go / no-go / scope" recommendation Shaw can act on.
