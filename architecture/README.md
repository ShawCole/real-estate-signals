# Architecture

Phase 2 deliverable.

| Doc | Status |
|---|---|
| [00-overview.md](00-overview.md) | **Done.** System diagram, data model, scraper interface, scoring, API, dashboard, stack, MVP timeline, costs, open decisions. |
| 01-scraper-framework.md | Pending — detailed plugin contract + the 5 vendor-pattern adapter specs (qPublic, Tyler, Accela, Fidlar, ArcGIS-REST). Written week 1. |
| 02-data-pipeline.md | Pending — normalize/dedupe/match-to-parcel/score logic + observability spec. Written week 1. |
| 03-database-schema.md | Pending — full DDL + migration plan (00-overview has the headline schema). |
| 04-scoring-model.md | Pending — full weights YAML + decay-function math. |
| 05-investor-ui.md | Pending — wireframes + interaction spec for the 6 dashboard pages. |

## Scope (locked from questionnaire `real-estate-signals-scope-v1`)

- **Build ourselves**, no Andy partnership
- **5 MVP counties:** Harris TX + Cuyahoga OH + Philadelphia PA + Cook IL + Maricopa AZ
- **No bulk licensing** (no ATTOM, no DataTree, no Regrid Enterprise)
- **One paid exception:** BatchData ($39-119/mo) for vacancy + skip-trace
- **MVP signals:** delinquency + vacancy only — code violations, probate, lis pendens added in post-MVP cohorts
- **GTM:** single-seat first, white-label second
- **Validation:** speculative but high-confidence — warm prospect pipeline

## Build path

4-week MVP. Then 2-week cohorts to add code violations → lis pendens → probate → deed transfers → expansion to 20 counties. Total: ~6 months from MVP start to full vision.

## Cost shape

- Build: $46-103K (one-time)
- Run: $26-55K/yr (down from $220-385K because of no-licensing pick)
- Breakeven: 22-46 single-seat users at $99/mo, or 2-3 white-label contracts at $1.5K/mo
