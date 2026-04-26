# Signal Latency Ranking — Reality → Ingested

**The question:** for each signal type, how long from when the real-world event happens until our DB has it?

**Components of total latency:**
1. **Inherent latency** — structural; some signals require N days of accumulated evidence before they exist (e.g. USPS vacancy requires 90 days of "no tenant" reports). Can't be reduced regardless of how fast we scrape.
2. **Publication latency** — source-side delay between event happening and being published.
3. **Ingestion latency** — our scrape cadence.

**Total = sum.** Sorted fastest-first.

---

## Tier 0 — Sub-hour to 24 hours (real-time signals)

These are the highest-value signals because we beat the rest of the market. Build these scrapers first.

| Signal | Reality | Best free source | Inherent | Publication | Our scrape | **Total** |
|---|---|---|---|---|---|---|
| **Foreclosure filing** (lis pendens / NOD / NTS) — judicial states | Lender decides + files | NC eCourts, FL myflcourtaccess, TX re:SearchTX | 0 | minutes | hourly poll | **<2 hours** |
| **Sheriff sale scheduled** (judicial states) | Court enters auction date | Same e-court systems | 0 | minutes | hourly | **<2 hours** |
| **HUD-owned home listed** | HUD lists REO | hudhomestore.gov | 0 | minutes | daily | **<24 hours** |
| **VA REO listed** | VA lists | vrmproperties.com | 0 | minutes | daily | **<24 hours** |
| **USDA Rural Dev REO listed** | USDA lists | properties.sc.egov.usda.gov | 0 | minutes | daily | **<24 hours** |
| **GSA Auction listed** | Federal property posted | gsaauctions.gov | 0 | minutes | daily | **<24 hours** |
| **OpenFEMA disaster declared** | FEMA declares | openfema.gov API | 0 | hours | daily | **<24 hours** |
| **News press release** (PR Newswire / Business Wire) | Company issues | RSS feeds | 0 | minutes | hourly | **<2 hours** |

**Why this tier matters:** these are the signals that PropStream/BatchLeads either don't carry or refresh weekly. A property card showing "NOD filed 47 minutes ago" beats one showing "NOD filed 4 days ago" every time.

---

## Tier 1 — 1-3 days

| Signal | Reality | Best free source | Inherent | Publication | Our scrape | **Total** |
|---|---|---|---|---|---|---|
| **Deed transfer recorded** (incl. quitclaim, $1, deed-in-lieu) | Parties sign + record | County recorder e-recording (Philly Records, Cook Recorder, Maricopa Recorder, Harris Clerk) | 0 | next biz day | daily | **1-2 days** |
| **NOD/NTS — non-judicial states** (TX, AZ, CA pattern) | Lender records | County recorder | 0 | next biz day | daily | **1-2 days** |
| **City code violation issued** — Socrata cities | Inspector cites | Cleveland (clevelandohio.gov), Chicago (data.cityofchicago.org), Philly (OpenDataPhilly), NYC (data.cityofnewyork.us) | 0 | 12-48h ETL | daily | **1-3 days** |
| **City code violation issued** — ArcGIS cities | Inspector cites | Charlotte (open.charlotte-gov), Detroit (data.detroitmi.gov), Houston (cohgis-mycity.opendata.arcgis.com) | 0 | 24-48h ETL | daily | **2-3 days** |
| **Maricopa lien/delinquent parcel posted** | Treasurer flags | Maricopa Treasurer ArcGIS Experience | 0 | next biz day | daily | **1-2 days** |
| **Harris JP eviction filed** | JP court entry | Harris JP Public Extracts (`/PublicExtracts/search.jsp`) | 0 | next biz day | daily | **1-2 days** |
| **Detroit Blight ticket issued** | Inspector cites | data.detroitmi.gov Improve Detroit | 0 | 24h | daily | **1-2 days** |
| **PACER bankruptcy filed** | Debtor files Ch7/Ch13 | PACER ($) | 0 | minutes | daily (cost-gated) | **1 day** |
| **SEC 8-K filed** (item 5.02 dep/app) | Material event | SEC EDGAR | 0 | within 4 biz days | daily | **1-4 days** |

**These are the workhorse signals** — fresh enough to act on, free, and the bulk of our wedge sits here (code violations).

---

## Tier 2 — 4-14 days

| Signal | Reality | Best free source | Inherent | Publication | Our scrape | **Total** |
|---|---|---|---|---|---|---|
| **City code violation** — Accela cities (Indianapolis, San Antonio) | Inspector cites | Accela ACA portals (HTML scrape) | 0 | 1-2 days | daily | **2-4 days** |
| **Building permits** — open / expired / demolition | Owner pulls or expiry | Most cities (Socrata + ArcGIS) | 0 | 1-7 days | daily | **2-8 days** |
| **Federal Register rule filed** | Agency publishes | FederalRegister.gov | 0 | 1-3 days | daily | **2-4 days** |
| **SAM.gov entity exclusion / registration change** | DoD/agency action | SAM.gov | 0 | 1-3 days | daily | **2-4 days** |
| **Repeat code violation accumulator** (3+ in 12mo flag) | 3rd violation issued | Same Socrata/ArcGIS as above | 0 | per-violation | daily | **same as code violation, 1-3 days** |
| **WARN Act mass-layoff notice** | Employer files 60-day notice | State DOL (TX uses Socrata, others vary) | 0 | 1-7 days | weekly | **3-14 days** |
| **HUD-USPS quarterly tract vacancy** — when current quarter drops | Quarter ends | huduser.gov USPS quarterly DBF | 0 | 30-60 day publish lag | quarterly poll | (see Tier 4) |

---

## Tier 3 — 2-6 weeks

| Signal | Reality | Best free source | Inherent | Publication | Our scrape | **Total** |
|---|---|---|---|---|---|---|
| **Tax delinquency** — modern continuous-publish counties | Owner misses payment | Cuyahoga, Cook, Maricopa, Philly Treasurers (online searchable) | 30-day grace period | next biz day after grace | weekly | **~5 weeks** (driven by 30-day grace) |
| **Probate filing** — counties with online dockets (NY NYSCEF, FL clerks, Cook IL ePM) | Decedent dies → executor files | Same e-court portals as Tier 0 | 30-180 days (executor delay) | next biz day | weekly | **30-180 days** (driven by family delay) — but file appears 1-2 days after filed |
| **City vacant property registered** — Cleveland VAPR, Chicago VBR, Detroit, Buffalo | Owner registers OR city auto-flags | Each city's open data | 30-day registration window | weekly publish | weekly | **5-7 weeks** |
| **Absentee owner derivation** (mailing ≠ property address) | Owner moved / inherited | Derived from county assessor scrape | 0 | annual roll refresh | weekly diff | **1-12 months** (driven by annual revaluation) |
| **PropertyRadar-style "free-and-clear" derivation** | Last mortgage paid off | Recorder satisfaction-of-mortgage filings | 0 | 1-7 days | weekly | **1-2 weeks** |

---

## Tier 4 — 1-3 months

| Signal | Reality | Best free source | Inherent | Publication | Our scrape | **Total** |
|---|---|---|---|---|---|---|
| **HUD-USPS quarterly tract vacancy** | Property genuinely vacant | huduser.gov | **90 days** (USPS requires 90d of "no tenant" reports) | 30-60 day publish lag | quarterly | **120-150 days** |
| **USPS NCOA via paid path** (BatchData, Regrid Enterprise) | Property genuinely vacant | BatchData $39-119/mo | **90 days** (same USPS rule) | 7-14 day vendor delivery | daily | **97-104 days** |
| **Annual tax delinquency for counties that publish only annually** | Owner missed annual cycle | Smaller counties (not in our MVP-5) | up to 365 days | 30-day publish | annual | **up to 1 year** |
| **Census ACS 5-year tract estimates** | ACS measurement period | data.census.gov | **5 years** (rolling 5-year averages) | ~12 month publish lag | annual | **5+ years** (context layer only) |
| **Tax sale auction calendar published** | County schedules annual sale | County treasurer + RealAuction/GovEase | depends on cycle | 30-90 day pre-sale notice | weekly | **30-90 days pre-event** |

---

## Tier 5 — Months to years (context layers only)

| Signal | Reality | Best free source | Total latency |
|---|---|---|---|
| **NAIP aerial imagery refresh** | Property changes visually | USDA NAIP | **2-3 years per state** |
| **Tax assessor revaluation** | Market shift | County assessor annual roll | **1 year** |
| **EPA brownfields ACRES update** | Contamination assessed | epa.gov ACRES | **months-years** |
| **FHFA House Price Index** | Market price moves | fhfa.gov | **1 quarter publish lag** |
| **BLS unemployment by metro** | Labor market shifts | BLS LAUS | **2 month publish lag** |

---

## Headline takeaways

1. **The fastest free signals are court e-filings + sheriff sales + HUD/VA/USDA REO listings.** Sub-24-hour latency, free, machine-readable. These are what differentiates us — PropStream/BatchLeads refresh these weekly at best.

2. **Vacancy has a structural floor of 90 days.** USPS requires 90 days of "no tenant" reports before any vacancy signal exists *anywhere* — paid or free. The HUD quarterly file adds another 30-60 days, BatchData adds 7-14 days. **No source on Earth gives you fresher than 90-day-old vacancy data**, because the data fundamentally doesn't exist before then. Implication: vacancy is a *confirmation* signal, not a *first-mover* signal. The first-mover signals are foreclosure filings and code violations.

3. **City code violations land in 1-3 days for Socrata cities, 2-4 days for Accela cities.** Combined with the fact that 87% of top-30 cities publish them, this is by far the best wedge: fresh, free, broad coverage, and existing platforms ignore it.

4. **Tax delinquency latency is ~5 weeks driven entirely by the 30-day grace period.** Once we know a parcel is delinquent, we know it from the first daily scrape after grace expires. The signal isn't fresh-fresh, but it's deterministic and decay-tier-3 anyway (months-of-distress, not days).

5. **The "fast tier" (0-3 days) maps cleanly to act-now Tier-1 distress score.** The "medium tier" (1-6 weeks) maps to Tier-2 watch-list. Tier 4-5 sources are context-only (don't drive scoring decisions; they enrich the property card).

---

## Implication for the architecture

The 4-tier signal-decay model in `architecture/00-overview.md` should weight signals by latency-to-act, not just by inherent severity:

| Decay tier | Original weighting | Latency-aware weighting |
|---|---|---|
| 1 — Act now | Severity high | Severity × (1 / latency-days) — heavy bonus for sub-24h freshness |
| 2 — 30-90 days | Severity med | Severity, latency neutral |
| 3 — Nurture | Severity low | Severity × log(latency) — older signals get diminishing returns |
| 4 — Context | Persistent | Severity × constant low weight |

The implementation: store `observed_at` (real-world event) AND `ingested_at` (when our scraper saw it) in the `signals` table. The score function computes `freshness = (now - observed_at)` and applies a decay curve specific to the signal type.

This is also a UX point: every property card surfaces "fresh in last 24h" badges on Tier-0 signals so investors instantly see what's actionable *right now* vs what's just background.
