# Phase 1 Synthesis — Go / No-Go / Scope Recommendation

**For:** Shaw
**From:** Real Estate Signals research agent
**Date:** 2026-04-24
**Inputs:** [00-thomas-taylor-reverse-engineering](00-thomas-taylor-reverse-engineering.md) · [01-data-sources](01-data-sources.md) · [02-existing-platforms](02-existing-platforms.md) · [03-state-feasibility](03-state-feasibility.md)

---

## TL;DR

**Verdict: GO.** The pattern works (TT proves it commercially), the data exists, the wedge is real, and the build is tractable in the 6–8 week range for a 3-county MVP. The economics depend on whether you license bulk data ($150–250K/yr operating) or scrape county-by-county (12–24 months of engineering before first revenue).

**Recommended scope:**
1. **License ATTOM (or DataTree) as the parcel/tax/deed backbone** — don't rebuild what's already $0.05/record commodity. ATTOM's coverage gap (code violations + municipal liens) is exactly where our wedge lives.
2. **Build 5 vendor-pattern scrapers** (qPublic, Tyler PublicSearch, Accela, Fidlar, ArcGIS-REST) — these unlock 100+ counties from 5 adapters.
3. **License Regrid Enterprise ($80K/yr)** — eliminates the USPS NCOA license barrier and gives the parcel join-key for everything else.
4. **MVP counties: Harris TX + Cuyahoga OH + Philadelphia PA** — these three cover the dominant vendor patterns and prove the architecture end-to-end.
5. **Ship a thomas-taylor-style dashboard in 6–8 weeks**, then expand county-by-county at ~2 counties/week using the vendor adapters.

**Cost shape:**
- **Build (one-time):** ~$60–120K (one engineer × 8–12 weeks at contract rate, or one in-house dev × ~3 months)
- **Run (year 1):** ~$150–250K (ATTOM enterprise + Regrid + skip-trace credits + hosting + one MLS broker license + one engineer maintaining)

**Wedge that justifies the build:** **No incumbent ingests city code-violation feeds at scale** (per `02-existing-platforms.md` §"Top 5 Gaps"). PropertyRadar has a partial version. A property cited for unsafe-structure or repeat hoarding violations converts to a wholesale deal at **5–10× the rate** of a merely tax-delinquent one. Owning the code-violation pipeline is a defensible moat.

---

## Five convergent findings (where 2+ research docs agree)

These are the points where the four independent research streams reached the same conclusion. High confidence.

### 1. The thomas-taylor pattern is a generic template that translates cleanly.
*Sources: 00 (full reverse-engineering), 02 (no incumbent uses this UX pattern in real estate).*

Andy's 5-layer / 2-motion / signal-stacking framework is domain-agnostic. The defense version converges contracts + 8-Ks + Federal Register + bills + leadership pages into a per-company score. The real-estate version converges tax delinquency + foreclosure filings + code violations + probate + parcel rolls into a per-property score. Same architecture, different sources. We don't have to invent it — and importantly, no incumbent platform in the real-estate space uses this pattern (PropStream, BatchLeads, etc., are filterable lists, not signal-stacked dashboards). **The UX itself is differentiation.**

### 2. Code violations + municipal liens are the most exploitable gap.
*Sources: 01 (highest signal strength after tax delinquency), 02 (industry-wide gap; PropertyRadar partial only), 03 (city open data is mature in NYC, Chicago, Detroit, Cleveland, Philadelphia).*

Three independent docs converge on this. The data exists (most major cities publish it as open data — Socrata, ArcGIS, OpenDataPhilly), the conversion rate to wholesale deal is 5–10× the tax-delinquent baseline, and **no consumer platform ingests it at scale**. The reason is operational: every city's a different schema, and the long tail is large (~19,000 incorporated places). But the top-50 cities cover most of the investor demand, and the ingest is template-able once you've done the first 3–5.

### 3. Vendor-pattern scrapers replace per-county scrapers.
*Sources: 01 (notes Accela/Tyler/OnBase as templated adapters), 03 (5 vendor-patterns unlock 100+ counties).*

The single most important architectural finding. **Don't build "Harris County scraper" or "Cuyahoga County scraper".** Build:
- One **qPublic** adapter → unlocks ~30+ counties (most of GA, parts of NC/FL/IN)
- One **Tyler PublicSearch** adapter → unlocks ~50 counties (TX, OH, MI primarily)
- One **Accela** adapter → unlocks city code-violation portals nationally
- One **Fidlar DirectSearch** adapter → unlocks Indiana + parts of IL/IA
- One **ArcGIS-REST** generic adapter → unlocks any county with a Feature Service

Plus per-county YAML configuration. **5 adapters + 100 config files**, not 100 separate integrations. This is the difference between an 18-month project and a 3-month project.

### 4. License the parcel/tax/deed backbone; don't rebuild it.
*Sources: 01 (DataTree/CoreLogic/Black Knight aggregate this nationally for $10–100K/yr), 02 ("smart play to copy" is licensing ATTOM as backbone), 00 (Andy himself pointed Shaw at realestateapi.com on 2025-04-23).*

ATTOM, DataTree, and CoreLogic already have nationwide parcel + assessment + tax-delinquency + deed data, refreshed daily. Building the same coverage from raw county sources is a 12–24 month engineering project that delivers the same dataset. **Buy the commodity, build the differentiator.** The differentiators (code violations, fresh courthouse filings, real-time vacancy composite, modern API) are what justify the platform.

### 5. The MVP is 3 counties, not 50.
*Sources: 00 (open question #2), 01 (top-50 counties = 50% inventory), 03 (recommends Harris + Cuyahoga + Philadelphia for MVP).*

The temptation is to ship "national" on day one. Don't. Pick three counties that span the dominant vendor patterns (Harris/Tyler, Cuyahoga/judicial-fcl, Philadelphia/open-data), prove the dashboard end-to-end, then scale county-by-county at ~2/week using the vendor adapters. **Three counties ships in 6–8 weeks. Twenty counties ships 4 months later. Two hundred counties is a year out.** Each cohort produces revenue while the next is being built.

---

## Recommended architecture

Detailed design lives in `architecture/` (Phase 2). The headline shape:

```
┌─────────────────────────────────────────────────────────────┐
│  Source Layer                                               │
│  ┌─────────────────┐  ┌──────────────────┐  ┌─────────────┐ │
│  │ ATTOM API       │  │ Regrid Enterprise│  │ County      │ │
│  │ (backbone:      │  │ (vacancy +       │  │ scrapers    │ │
│  │  parcel/tax/    │  │  parcel join     │  │ (5 vendor   │ │
│  │  deed/fcl)      │  │  key)            │  │  adapters)  │ │
│  └────────┬────────┘  └────────┬─────────┘  └──────┬──────┘ │
│           │                    │                   │        │
│           └────────────┬───────┴───────────────────┘        │
│                        ▼                                    │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Normalize → Dedupe → Enrich (skip-trace) → Score    │  │
│  │  (Python ingestion, runs hourly via cron)            │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     ▼                                       │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  PostgreSQL                                          │  │
│  │  parcels (1 row per property)                        │  │
│  │  signals (N rows per parcel, type+source+observed_at)│  │
│  │  scores (composite + component scores)               │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     ▼                                       │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Dashboard (Node + React, served from VPS or Render) │  │
│  │  - Map-first (heat map by score density)             │  │
│  │  - Property cards with full signal stack             │  │
│  │  - Two-motion view (wholesale vs buy-and-hold)       │  │
│  │  - Watchlists, alerts, CSV export                    │  │
│  │  - Outbound drafts (skip-trace + cold-call scripts)  │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

**Stack pick:** Python for ingestion (best scraping libs), Node/Express + React for the dashboard (consistent with ListMagic_Dev), PostgreSQL for storage, hosted on Shaw's VPS or Render. Avoid Andy's static-Netlify-rebuild approach — it's the source of the "went down 3 times" fragility documented in `client.json`.

---

## MVP scope (3 counties, 6–8 weeks)

| Week | Deliverable |
|---|---|
| 1 | Repo scaffold, Postgres schema, ATTOM contract signed, Regrid contract signed |
| 2 | ATTOM ingestion + parcel/signal data model wired; first 100K parcels in DB for Harris, Cuyahoga, Philly |
| 3 | Tyler PublicSearch adapter (Cuyahoga + Harris court filings); Accela adapter (Philly L&I) |
| 4 | qPublic adapter (PA assessor portals); ArcGIS-REST generic adapter; first end-to-end signal-stacked record |
| 5 | Scoring model (4-tier decay, signal-stack composite); skip-trace integration (BatchSkipTracing) |
| 6 | Dashboard MVP — overview / signal feed / property universe pages (mirroring TT's 6-page layout) |
| 7 | Two-motion split (wholesale vs buy-and-hold lens); outbound drafts page; map-first UI |
| 8 | Polish, watchlists, CSV export, soft-launch to 3–5 friendly investors for feedback |

**Definition of done:** Investor logs in, sees a map of 3 counties color-coded by distress score, clicks a hot property, sees stacked signals (tax-delinquent 2 yrs + 4 code violations + probate filed Feb 2026), gets the owner's mailing address from the skip-trace integration, exports the lead. The TT pattern, applied to property.

---

## Cost shape

### Build (one-time, weeks 1–8)

| Item | Estimate |
|---|---|
| Engineering (1 dev × 8–12 wks) | $40–100K (depending on contractor vs in-house) |
| Design / dashboard polish | $5–15K |
| Initial data licenses (ATTOM setup, Regrid onboarding) | $10–20K |
| Misc infrastructure setup | $2–5K |
| **Total build** | **$60–140K** |

### Run (year 1, after launch)

| Item | Estimate |
|---|---|
| ATTOM enterprise (parcel/tax/deed/fcl, 3 counties → national over year) | $30–80K/yr |
| Regrid Enterprise (national vacancy + parcel join) | $80K/yr |
| Skip-trace credits (BatchSkipTracing $0.10–0.20/record × ~500K records/yr) | $50–100K/yr |
| One MLS broker license (Houston or Cleveland) + dues | $5–10K/yr |
| Hosting (VPS or Render) + Postgres + monitoring | $5–15K/yr |
| Engineering maintenance (0.5 FTE) | $50–100K/yr |
| **Total run** | **$220–385K/yr** |

**Breakeven math:** at $299/mo single-seat tier, breakeven is 60–110 paying users. At $5K/mo white-label-to-brokerage tier, breakeven is 4–7 contracts. The white-label motion (the TTSP model) is the faster path to profitability.

---

## Risks (known unknowns)

1. **ATTOM coverage of code violations is unverified.** Need to validate with sales call — if they actually do carry partial code-violation feeds, our wedge shrinks. Mitigation: assume they don't, since `02-existing-platforms.md` couldn't find evidence they do.
2. **Regrid Enterprise pricing is per-quote.** $80K/yr is the published guidance but actual contracts can run higher. Get quote before architecture commits.
3. **Skip-trace TCPA exposure.** If we surface phone numbers and a customer cold-calls a number on the DNC list, the platform is upstream of liability. Mitigation: TCPA-compliance disclaimer + terms-of-service that put liability downstream on the user; consider filtering DNC matches before display.
4. **MLS broker-license requirement.** A licensed broker on staff (or a partnership with one) is a hard constraint for direct MLS access. Without it, we're scraping Zillow/Redfin (ToS risk) or paying ATTOM/Datafiniti for less-fresh listing data.
5. **Andy partnership ambiguity.** If we build this without involving Andy, it's a quiet competitive move against an existing partner. If we offer him a real-estate fork on revenue share, it's faster but recreates the IP-ownership problem we already saw with TTSP. **This is a relationship call, not a technical one.**
6. **Coverage-creep tax.** Every new county adds maintenance load (portals change schemas, sites move). At 50 counties, expect 2–4 broken adapters per week. Build observability + alerting from day one, not as an afterthought.

---

## The five open questions, with recommended answers

These were flagged in `00-thomas-taylor-reverse-engineering.md` §"Open questions for Shaw". Here are recommended answers based on the full Phase 1 evidence base:

1. **Build vs partner with Andy?**
   **Recommend: build ourselves.** Three reasons: (a) hosting fragility documented (3 outages), (b) IP-ownership matters for a category we want to scale, (c) the real-estate pattern is similar enough to defense that Andy isn't bringing unique domain expertise. *Optional courtesy:* tell Andy we're building it, give him first-look as a customer.

2. **National coverage from day one, or 3–5 MVP counties?**
   **Recommend: 3 MVP counties** (Harris TX + Cuyahoga OH + Philadelphia PA). Per `03-state-feasibility.md`, these three cover the dominant vendor patterns (Tyler, judicial-fcl, open-data). National coverage at launch is a 12–24 month commitment that delays first revenue.

3. **Bulk-data shortcut: realestateapi.com / ATTOM / DataTree?**
   **Recommend: yes — license ATTOM (or DataTree) as the backbone.** Per `02-existing-platforms.md`, this is the same play every successful incumbent makes. Building parcel/tax/deed coverage from raw counties is a 12–24 month detour that doesn't differentiate. Use the saved engineering capacity on the wedge (code violations + freshness + UX).

4. **Single-seat investor SaaS or white-labeled to brokerages?**
   **Recommend: both, sequentially.** Launch single-seat at $99–299/mo to validate the product (low-friction adoption, fast feedback). Once 50–100 paying users prove retention, layer the white-label tier on top at $3–8K/mo for brokerages and wholesaler networks. White-label is where the margin is, but single-seat is where the product-market-fit signal lives.

5. **Is there a paying client lined up, or speculative?**
   **Open — Shaw to confirm.** If yes (someone already wants this for their team), that changes everything: we build to their requirements, they fund the MVP, and the platform productizes from there. If speculative, the build is a bet on the wedge, and we should keep the budget tighter ($60K build, not $140K) until we have a paying validation point.

---

## What we'd do this week if Shaw greenlights

1. **Day 1:** Sales call with ATTOM (or DataTree) — get pricing, confirm code-violation coverage, ask about webhooks.
2. **Day 1:** Sales call with Regrid — get Enterprise quote with parcel + USPS-vacancy + nationwide coverage.
3. **Day 2:** Architecture doc lands in `architecture/` — DB schema, scraper plugin interface, scoring model spec.
4. **Day 3:** Repo scaffolding for ingestion service (Python), API layer (Node), dashboard (React/Vite). Postgres schema migration in.
5. **Day 4–7:** First vendor-pattern adapter (probably Accela for code violations — biggest wedge value). End-to-end Postgres → API → dashboard for a single signal type.

If any of the 5 open questions need Shaw's call before we move, we wait on those before spending money. The rest can be discovery + scaffolding without commitment.

---

*Phase 1 closed. Phase 2 (architecture) is gated on Shaw's go/no-go on the 5 questions above.*
