# Research

Phase 1 deliverables — **complete**.

Read the synthesis first, then drill into the source docs as needed.

| Doc | What it answers |
|---|---|
| [04-synthesis.md](04-synthesis.md) | **Start here.** Go/no-go verdict, recommended scope, MVP plan, cost shape, 5 open questions with recommended answers. |
| [00-thomas-taylor-reverse-engineering.md](00-thomas-taylor-reverse-engineering.md) | What Andy already built for TTSP, how it works, and how the pattern translates to real estate. Includes Andy's verbatim architecture spec from the WhatsApp convo. |
| [01-data-sources.md](01-data-sources.md) | Catalog of every public real-estate signal source (12 sections + 27-row synthesized table). Top-3 highest-signal sources to build first. |
| [02-existing-platforms.md](02-existing-platforms.md) | Competitive analysis of 12 platforms (PropStream, BatchLeads, DealMachine, ATTOM, etc.) with pricing table, coverage matrix, and top-5 market gaps. |
| [03-state-feasibility.md](03-state-feasibility.md) | County-by-county scraper feasibility for top 10 investor states. Greenlight/Yellow/Red verdicts and the vendor-pattern insight (5 adapters unlock 100+ counties). |

## The 4 convergent findings

1. The thomas-taylor pattern is generic and translates cleanly — same architecture, swap the source layers.
2. **Code violations + municipal liens are the most exploitable gap** (no incumbent ingests city building-dept data at scale).
3. Build **5 vendor-pattern scrapers** (qPublic, Tyler, Accela, Fidlar, ArcGIS-REST), not per-county scrapers — collapses the build from 100+ integrations to 5 adapters + 100 config files.
4. **License the parcel/tax/deed backbone** (ATTOM or DataTree), don't rebuild it — every successful incumbent does this.

## Phase 2 status

**Blocked on Shaw's go/no-go** on the 5 open questions in `04-synthesis.md` §"The five open questions".
