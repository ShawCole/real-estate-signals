# California — Analysis & Latency Map

**Source catalog:** [`sources/06-california.md`](sources/06-california.md) — 261 verified rows (45 state + 158 county + 58 city)
**Date:** 2026-04-26

CA is its own animal. Same scraper architecture works, but the *coverage map* is different from the rest of the country in three structural ways. Plus there are 2-3 CA-only signals that no national vendor exploits.

---

## TL;DR

1. **The CA recorder paywall is a structural problem in the 4 biggest investor markets.** LA, San Diego, Riverside, San Bernardino — by population the 4 biggest CA counties — gate online recorder access (LA via Govt Code §6254.21 prohibition on online grantor/grantee posting; the others via paid 3rd-party / ParcelQuest / Cloudflare). This collapses the foreclosure-filing wedge in **exactly the markets investors care about most** in CA. Workarounds exist but they're either paid (ParcelQuest), brittle (Playwright + residential proxies), or slow (PRA requests).

2. **The Bay Area + Orange County are wide-open.** SF, Alameda, Orange, Sacramento all publish full free online grantor/grantee + NOD/NTS recordings. So we can ship CA at full quality in those 4 markets *immediately* — and accept reduced foreclosure coverage in the SoCal Inland Empire markets, leaning on tax delinquency + code violations + insurance non-renewal instead.

3. **Two CA-only signals are worth building scrapers for, full stop.** **CDI insurance non-renewal data** (CA Dept of Insurance publishes ZIP-level wildfire-zone non-renewal counts annually + post-fire mandatory-moratorium ZIP lists — overlay with CalFire perimeters and CA FAIR Plan exposure to predict forced-sale clusters). **CDIAC Mello-Roos default reports** (CA-unique; CFD bond defaults trigger judicial foreclosure under Govt Code §53356.1 in **150 days** — 4-5× faster than regular tax-default foreclosure; virtually no national vendor exploits this).

4. **Anaheim has the best city-level latency in the entire country we've cataloged.** Anaheim refreshes code-enforcement cases from Accela every 15 minutes to a public ArcGIS REST endpoint. Sub-hour latency on code violations. Single most monitorable city for live distress signals.

---

## CA-specific signal latency ranking

Same tier structure as `research/06-signal-latency-ranking.md`, with CA-specific sources substituted.

### Tier 0 — Sub-hour to 24 hours

| Signal | Best free CA source | Total latency | Notes |
|---|---|---|---|
| **Code violations — Anaheim** | Anaheim Accela → ArcGIS REST | **<1 hour** | Best in entire US — 15-minute refresh |
| **Eviction notices — SF** | DataSF Eviction Notices `5cei-gny5` | <24h | Daily Socrata refresh |
| **Building inspection complaints — SF** | DataSF `gm2e-bten` | <24h | Daily |
| **Building permits — SF** | DataSF `i98e-djp9` | <24h | Daily |
| **CalFire active wildfire perimeters** | CalFire ArcGIS Hub | <1 hour | Real-time during fire events |
| **Insurance non-renewal — moratorium ZIPs (post-fire)** | CDI mandatory-moratorium ZIP list | <24h post-declaration | Triggered by Governor's emergency declaration |

**No CA court e-filing is in this tier** — CA superior courts use a fragmented mix of Tyler Odyssey (some), Journal Technologies (LA), and proprietary systems. There is **no statewide unified case search**. Per-county adapters required.

### Tier 1 — 1-3 days

| Signal | Best free CA source | Total latency | Notes |
|---|---|---|---|
| **NOD / NTS recorded — SF, Alameda, Orange, Sacramento** | County recorder online grantor/grantee | 1-2 days | Same as TX/AZ pattern |
| **NOD / NTS recorded — LA, SD, Riv, SB** | ❌ Paywall (ParcelQuest, NETR, in-person) | n/a — paid | The structural problem |
| **Code violations — DataSF (SF), data.lacity.org (LA)** | Socrata SODA | 1-3 days | Daily ETL |
| **Code violations — Long Beach, Oakland, San Diego** | Socrata SODA / ArcGIS Hub | 1-3 days | All Tier-1 capable |
| **Tax-defaulted property list — Orange County** | OC Treasurer-Tax Collector | 1-3 days | Quarterly major refresh + interim updates |
| **Sheriff sale schedule — most counties** | County sheriff sites + Bid4Assets / GovEase | 1-3 days | LA moved to GovEase in 2026 |

### Tier 2 — 4-14 days

| Signal | Best free CA source | Total latency |
|---|---|---|
| **Building permits — most cities** | Socrata + ArcGIS Hub | 2-7 days |
| **Code violations — Accela cities (Bakersfield, Fresno)** | HTML scrape of Accela Citizen Access | 2-4 days |
| **EDD WARN Act mass layoff** | edd.ca.gov publication | 3-14 days |
| **CA Superior Court civil cases** | Per-county portals (Tyler Odyssey where used) | 2-7 days (varies wildly by county) |

### Tier 3 — 2-6 weeks

| Signal | Best free CA source | Total latency |
|---|---|---|
| **Tax delinquency — modern counties (Alameda, OC, SF, Sac)** | County tax collector | ~5 weeks (driven by 30-day grace + scrape cadence) |
| **Probate filings — counties with online dockets** | Per-county; **no statewide search** | 30-180 days from death |
| **Mello-Roos CFD default — initial flag** | CDIAC quarterly Default & Draw on Reserve report | 30-90 days (quarterly publish) |
| **Code violations — repeat-offender accumulation flag** | Same Socrata feeds; aggregate over time | 1-3 weeks |

### Tier 4 — 1-3 months

| Signal | Best free CA source | Total latency | Notes |
|---|---|---|---|
| **Mello-Roos CFD foreclosure trigger** | CDIAC default reports → trustee filings 150 days post-default | **150 days** | CA-unique 4-5× speedup vs regular tax-default |
| **Insurance non-renewal — annual ZIP-level counts** | CDI annual report | 6-12 months publish lag | Slow but uniquely CA |
| **HUD-USPS quarterly tract vacancy** | huduser.gov | 120-150 days | Same as elsewhere |
| **CA tax-default 5-year wait** | County treasurer "Power to Sell" lists | **5 years** | This is why CA tax-defaulted-auction inventory is a slow trickle |

### Tier 5 — Months to years (context only)

| Signal | Source |
|---|---|
| Prop 13 / Prop 19 reassessment events | Per-county assessor — annual roll, 1-year lag |
| CDIAC annual debt issuance reports | Annual |
| CalFire historic damage perimeters | Annual |

---

## CA county prioritization map

Combining latency + free-data depth + investor market size:

### Tier A — ship CA MVP with these 4

| County | Why | Caveats |
|---|---|---|
| **San Francisco** | DataSF is the deepest open-data portal in CA. Eviction notices + permits + violations + 311 all queryable via SoQL. | Small market (one city, ~860K population). |
| **Orange County** | Free RecorderWorks grantor/grantee since 1982 + free Bid4Assets/in-house tax sales + Hub-style GIS. Major investor market. | Bid4Assets shifted some auctions; verify current platform. |
| **Alameda County** | Free OPR online recorder since 1969 + Tyler eCourt Odyssey + ArcGIS Hub. Bay Area market. | None major. |
| **Sacramento County** | Strong open data + capital market = lots of state-employee absentee owners | Less investor-heavy than SoCal but very buildable. |

### Tier B — high-value but recorder paywall — defer or accept reduced wedge

| County | Why valuable | Recorder workaround |
|---|---|---|
| **Los Angeles County** | Largest distressed inventory in the state; LADBS code violations are excellent (Socrata-backed) | LA recorder is paywalled by Govt Code §6254.21 — **statutory prohibition**, not just a vendor choice. Workarounds: ParcelQuest ($), NETR Online ($), Playwright scrape of paid sites (ToS gray). For MVP: ship LA with **code violations + tax delinquency + insurance non-renewal only**, defer foreclosure-filing wedge. |
| **San Diego County** | Major investor market | Recorder gated behind paid 3rd-party. Same workaround as LA. |
| **Riverside / San Bernardino** | Inland Empire — major flip + buy-and-hold market | Recorder paywalled. Ship with non-recorder signals only. |

### Tier C — defer entirely until Tier A is shipping

Santa Clara, Contra Costa, San Mateo, Fresno, Kern, Ventura — fine open data, but smaller distressed inventory.

---

## CA cohort recommendation

If you want to add CA to the post-MVP cohort plan in `architecture/00-overview.md`:

**Cohort 9 (after MVP-5 cohorts 1-8 ship): California "wide-open" rollout — 4 counties.**

- SF + Alameda + Orange + Sacramento, ~3 weeks
- All 5 signal types: tax delinquency + vacancy + code violations + foreclosure filings (NOD/NTS at recorder) + probate
- Add **CA-specific signal: insurance non-renewal overlay** (state-level CDI feed scoped per ZIP within these 4 counties)

**Cohort 10: California "paywalled" rollout — LA + SD + Riv + SB, with reduced wedge.**

- 4 counties, ~3 weeks
- Signals: tax delinquency + code violations + vacancy (HUD-USPS tract) + insurance non-renewal — **skip foreclosure-filing wedge**
- Add **CA-only Mello-Roos CFD default tracking** via CDIAC (statewide; ~150 day foreclosure trigger)
- Add **Anaheim live-feed** (15-min refresh code violations) as a marquee feature

**Total CA build: ~6 weeks for 8 counties + 2 CA-unique signals.**

---

## Why this matters for the platform pitch

CA is where you can credibly tell PropertyRadar (which is CA-headquartered and CA-strong): "we have signals you don't." Specifically:

1. **CDIAC Mello-Roos default tracking** — PropertyRadar doesn't expose this. It's a 150-day foreclosure clock that nobody's monitoring.
2. **CDI insurance non-renewal overlay** — post-wildfire forced-sale prediction is a story only we tell.
3. **Anaheim 15-minute code-violation feed** — fastest urban distress refresh in the country.

Outside CA, our wedge is "we have code violations." In CA, the wedge is "we have **CA-specific distress vectors** PropertyRadar doesn't track despite being the CA specialist."

---

## Open questions surfaced by the CA dive

1. **LA County recorder workaround call.** ParcelQuest costs ~$50-200/mo; PropStream uses them. NETR Online is $20/mo. Either violates the no-licensing rule but is meaningfully cheaper than the BatchData spend we already greenlit. Worth a separate decision.
2. **CDIAC URL family doesn't resolve from this VPS** (DNS NXDOMAIN). Verified live elsewhere; treat as real but plan to scrape from a different egress IP.
3. **CA superior court fragmentation is permanent.** Unlike NC (eCourts statewide) or TX (re:SearchTX), CA has no statewide trial-court search and shows no sign of building one. Per-county adapters forever.
4. **Should CA be in the MVP-5 instead of Cook IL or Maricopa AZ?** Argument for: LA County alone has more distressed inventory than Cook + Maricopa combined. Argument against: the recorder paywall in LA reduces the wedge, while Cook + Maricopa give us cleaner full-stack signal coverage to validate the product.

If you want me to spin a 4-question scope questionnaire on the CA decisions (especially #1 and #4), say the word.
