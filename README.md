# Real Estate Signals

A signal platform for **deprecated, distressed, pre-foreclosure, tax-delinquent, and otherwise undervalued real estate** — surfacing properties worth exploring before they hit the mainstream investor market.

## Why this exists

The competitive edge in real estate investing is **time + signal**. Once a deal is on Zillow, Redfin, or even MLS, you're competing with thousands of buyers. The real opportunities are upstream: properties whose owners are about to be forced into a sale (tax delinquency, foreclosure, probate, code violations, divorce, vacancy decay) but where that signal hasn't yet been aggregated, scored, and surfaced to the right buyer.

PropStream / BatchLeads / DealMachine already do this — but they are walled gardens with $100-300/mo subscriptions, opaque data sourcing, and (per investor forums) significant gaps in coverage and freshness. There is room to build something more transparent, more granular, and more focused on the highest-signal indicators.

## Approach

Work the problem **county-by-county**, not nationally. Real estate data is fundamentally a county-level dataset — every parcel, lien, deed, and tax record lives in a county recorder/assessor system. The platform is a federation of per-county scrapers feeding a normalized property graph, with a composite scoring model on top.

## Repo layout

```
research/        Phase 1 — landscape mapping, competitor analysis, state feasibility
architecture/    Phase 2 — system design, schemas, scoring model
scrapers/        Phase 3 — per-county scrapers (modular plugins)
data/            Sample/test data, fixtures
scripts/         Pipeline / ingestion / scoring code
```

## Status

**Phase 1 — Research** (in progress).

See `research/` for current findings.

## Operating constraints

- Per Shaw: **no live scraping** until Phase 1 research is reviewed and architecture is approved.
- All findings push to this repo as markdown so future-Shaw / future-agents can audit the reasoning.
- Internal tool — owner is Shaw Cole / ArkData. Not a client deliverable.
