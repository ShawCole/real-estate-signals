# Scrapers

Phase 3 deliverables. **Not started — waiting on Phase 1 + 2.**

Per-county plugins. Naming convention: `{source_type}_{county}_{state}.py` — e.g., `tax_delinquent_harris_tx.py`, `lis_pendens_cuyahoga_oh.py`.

Each scraper implements a standard interface (defined in `architecture/01-scraper-framework.md` once written) and emits records conforming to the unified property + signal schema.

**No live scraping until Shaw approves the architecture.** This dir exists so the structure is in place when we're ready to build.
