# Source × Field Coverage Matrix

Reads as: **for each data source we'd ingest, which property-data fields does it carry?** Sources are grouped by latency tier (fastest first). Cells use:

- **✓** — always or near-always present in the source record
- **◐** — sometimes present (varies by jurisdiction or filing type)
- **✗** — not in this source (need to join from elsewhere)

**Columns:**

| # | Column | Why it's tracked |
|---|---|---|
| 1 | Address | Parcel address — primary join key |
| 2 | Owner | Name of record |
| 3 | Mailing | Owner mailing address (absentee-derivation key) |
| 4 | Contact | Phone / email |
| 5 | Value | Property value (assessed, appraised, or market) |
| 6 | Mortgage | Outstanding mortgage / loan amount |
| 7 | Liens | Other recorded liens (mechanic's, judgment, IRS, state tax, CFD) |
| 8 | Bankruptcy | Bankruptcy filing status |
| 9 | Status | Occupancy / vacancy / condition |
| 10 | Chars | Physical chars: beds, baths, sqft, year built, lot size |
| 11 | LastSale | Last sale date + price |
| 12 | TaxDel | Tax delinquency amount + years |
| 13 | Auction | Scheduled auction date |
| 14 | Notes | Anything notable |

---

## Tier 0 — Sub-24-hour latency

| Source | Addr | Owner | Mailing | Contact | Value | Mortgage | Liens | Bankr | Status | Chars | LastSale | TaxDel | Auction | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Court e-filings** (NC eCourts, FL myflcourtaccess, TX re:SearchTX) — foreclosure filings | ✓ | ✓ | ◐ | ✗ | ✗ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | Foreclosure NOD/NOTS = mortgage balance + sale date |
| **HUD Home Store** (REO listings) | ✓ | ◐ | ✗ | ✗ | ✓ | ✗ | ✗ | ✗ | ✓ | ✓ | ◐ | ✗ | ✗ | HUD is current owner; vacant by definition |
| **VA REO / USDA Rural Dev REO** | ✓ | ◐ | ✗ | ✗ | ✓ | ✗ | ✗ | ✗ | ✓ | ✓ | ◐ | ✗ | ✗ | Same shape as HUD Home Store |
| **GSA Auctions / Treasury Forfeiture** | ✓ | ✗ | ✗ | ✗ | ✓ | ✗ | ✗ | ✗ | ✓ | ✓ | ✗ | ✗ | ✓ | Federal seized property |
| **OpenFEMA disaster declarations** | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | County/ZIP polygon — context layer only |
| **CalFire wildfire perimeters** | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Polygon overlay |
| **CDI insurance moratorium ZIPs** (CA) | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ZIP-level only |
| **News RSS / press releases** | ◐ | ◐ | ✗ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Variable — case-by-case |
| **Anaheim code violations** (15-min refresh ArcGIS) | ✓ | ◐ | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Sub-hour latency; needs assessor join for value |

---

## Tier 1 — 1-3 day latency

| Source | Addr | Owner | Mailing | Contact | Value | Mortgage | Liens | Bankr | Status | Chars | LastSale | TaxDel | Auction | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **County recorder — deed transfers** (disclosure states) | ✓ | ✓ | ◐ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | ✗ | ✗ | The transfer IS the value-of-record event |
| **County recorder — deed transfers** (TX, AK, ID, KS, MS, MT, NM, ND, UT, WY non-disclosure) | ✓ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ (date only) | ✗ | ✗ | TX deed = no price; need HCAD appraised value |
| **County recorder — mortgage recordings** | ✓ | ✓ | ◐ | ✗ | ✗ | ✓ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Mortgage = lien type + face amount |
| **County recorder — NOD / NOTS / lis pendens** (non-judicial states: TX, AZ, CA) | ✓ | ✓ | ◐ | ✗ | ✗ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | Trustee-sale notice carries auction date |
| **County recorder — other liens** (mechanic's, judgment, IRS, state tax) | ✓ | ✓ | ◐ | ✗ | ✗ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Lien face amount disclosed |
| **City code violations — Socrata** (Cleveland, Chicago, Philly, NYC, LA, Long Beach, Oakland, San Diego) | ✓ | ◐ | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Some violations record as liens |
| **City code violations — ArcGIS Hub** (Charlotte, Detroit, Houston, Anaheim) | ✓ | ◐ | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Same shape as Socrata |
| **DataSF eviction notices** (SF) | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Tenant-side; LL info present |
| **Maricopa lien / delinquent ArcGIS** | ✓ | ✓ | ◐ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✓ | ◐ | Includes assessed value + delinquency amount |
| **Harris JP eviction extracts** | ✓ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | LL + tenant + judgment outcome |
| **Detroit Blight tickets** (data.detroitmi.gov) | ✓ | ◐ | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Strongest free vacancy proxy in US |
| **PACER bankruptcy** (Schedule A/B/D/E) | ◐ | ✓ | ✓ | ◐ | ◐ | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ | ◐ | ✗ | $0.10/page; the only source for federal bankruptcy |
| **SEC 8-K filings** (corporate property holdings) | ◐ | ✓ (entity) | ✓ | ✗ | ◐ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Mostly relevant for entity-owned RE |
| **Sheriff sale listings** (judicial states) | ✓ | ✓ | ◐ | ✗ | ◐ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ◐ | ✓ | Min bid + judgment amount |
| **Bid4Assets / GovEase / RealAuction tax-sale listings** | ✓ | ◐ | ✗ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ◐ | ✗ | ✓ | ✓ | Assessed value + min bid + delinquency |

---

## Tier 2 — 4-14 day latency

| Source | Addr | Owner | Mailing | Contact | Value | Mortgage | Liens | Bankr | Status | Chars | LastSale | TaxDel | Auction | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **City code violations — Accela ACA** (Indianapolis, San Antonio, Bakersfield, Fresno) | ✓ | ◐ | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | HTML scrape — needs Playwright |
| **Building permits** — open / expired / demolition | ✓ | ✓ | ◐ | ◐ | ◐ | ✗ | ✗ | ✗ | ✗ | ◐ | ✗ | ✗ | ✗ | Permit declared cost = improvement signal |
| **Federal Register filings** (DoD, DHS, State, BIS, EPA) | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Regulatory context — no parcel link |
| **SAM.gov entity exclusions / registrations** | ✗ | ✓ (entity) | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Entity-level — useful for commercial RE |
| **WARN Act mass-layoff notices** | ✗ | ✓ (employer) | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Employer address = potential commercial vacancy upcoming |
| **Repeat-violation accumulator** (3+ in 12mo flag) | ✓ | ◐ | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Derived signal — flags chronic owner |
| **CA superior court civil cases** (per-county Tyler/JT) | ◐ | ✓ (parties) | ◐ | ✗ | ✗ | ◐ | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | Eviction / divorce / partition |

---

## Tier 3 — 2-6 week latency

| Source | Addr | Owner | Mailing | Contact | Value | Mortgage | Liens | Bankr | Status | Chars | LastSale | TaxDel | Auction | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **County tax-delinquency lists** (modern continuous-publish counties: Cuyahoga, Cook, Maricopa, Philly, OC, Alameda, SF, Sac) | ✓ | ✓ | ◐ | ✗ | ◐ | ✗ | ✓ (tax lien) | ✗ | ✗ | ✗ | ✗ | ✓ | ◐ | The deterministic distress signal |
| **Probate filings** (NYSCEF NY, FL clerks, Cook ePM) | ◐ | ✓ (decedent) | ◐ | ✗ | ◐ | ◐ | ◐ | ✗ | ◐ | ✗ | ✗ | ✗ | ✗ | Estate inventory sometimes lists property + appraised value |
| **City vacant property registries** (Cleveland VAPR, Chicago VBR, Detroit, Buffalo, NYC HPD) | ✓ | ✓ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | Cleveland VAPR requires owner contact at registration |
| **Absentee-owner derivation** (mailing ≠ property) | ✓ | ✓ | ✓ | ✗ | ◐ | ✗ | ✗ | ✗ | ◐ | ◐ | ✗ | ✗ | ✗ | Derived from assessor roll diff |
| **Free-and-clear derivation** | ✓ | ✓ | ◐ | ✗ | ◐ | ✓ (zero) | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Derived from satisfaction-of-mortgage filings |
| **CDIAC Mello-Roos initial default flag** (CA) | ◐ | ✗ | ✗ | ✗ | ◐ | ✗ | ✓ (CFD) | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | CA-only; precursor to 150-day judicial foreclosure |

---

## Tier 4 — 1-3 month latency

| Source | Addr | Owner | Mailing | Contact | Value | Mortgage | Liens | Bankr | Status | Chars | LastSale | TaxDel | Auction | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **HUD-USPS quarterly tract vacancy** | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ (tract aggregate) | ✗ | ✗ | ✗ | ✗ | Census-tract level only — no parcel detail |
| **BatchData USPS NCOA via API** ($39-119/mo) | ✓ | ◐ | ✓ | ◐ | ✗ | ✗ | ✗ | ✗ | ✓ (parcel) | ✗ | ✗ | ✗ | ✗ | Same 90-day USPS rule applies; bundles skip-trace |
| **Mello-Roos foreclosure trigger** (CA, 150 days post-default) | ✓ | ◐ | ◐ | ✗ | ◐ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ◐ | ✓ | CA-unique; 4-5× faster than regular tax-default fcl |
| **CDI insurance non-renewal annual** (CA) | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ZIP-level — context only |
| **Annual tax delinquency** (smaller counties publishing 1-2x/yr) | ✓ | ✓ | ◐ | ✗ | ◐ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✓ | ◐ | Stale freshness, deterministic content |

---

## Tier 5 — Months to years (context + backbone)

| Source | Addr | Owner | Mailing | Contact | Value | Mortgage | Liens | Bankr | Status | Chars | LastSale | TaxDel | Auction | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **County assessor roll** (annual revaluation) | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ | ◐ | ✗ | ✗ | ✓ | ✓ | ◐ | ✗ | **THE BACKBONE.** Every Tier-2 signal joins to this. CA Prop 13 makes assessed value structurally stale. |
| **MLS RESO Web API** (broker-licensed, ~$5-10K/yr per region) | ✓ | ◐ | ◐ | ◐ | ✓ | ✗ | ✗ | ✗ | ◐ | ✓ | ✓ | ✗ | ✗ | Listing agent contact, not owner. Highest-fidelity property chars. |
| **NAIP aerial imagery** | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ◐ (overgrown lawn) | ✗ | ✗ | ✗ | ✗ | 2-3 yr refresh per state; vegetation overgrowth detection |
| **EPA brownfields ACRES** | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ◐ (contaminated) | ✗ | ✗ | ✗ | ✗ | Distress context |
| **FHFA House Price Index** | ✗ | ✗ | ✗ | ✗ | ✓ (MSA aggregate) | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | AVM calibration baseline |
| **Census ACS — housing characteristics + median value** | ✗ | ✗ | ✗ | ✗ | ✓ (tract aggregate) | ✗ | ✗ | ✗ | ✓ (tract aggregate) | ✗ | ✗ | ✗ | ✗ | Comparable-context data |
| **BLS unemployment by metro** | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | Economic distress context |

---

## Cross-cutting takeaways

### Where the data is densest

The richest single source per field:

| Field | Single best source | Notes |
|---|---|---|
| Address | All Tier-0/1/2 sources | Universal |
| Owner name | County assessor + recorder | Both required for completeness |
| Mailing address | **County assessor roll** | Annual refresh; absentee-derivation key |
| Contact (phone/email) | **PACER Schedule A/B (debtor's contact)** + **city vacant registries** + **paid skip-trace** | Free options are weak; BatchData $0.10-0.20/lookup is the realistic path |
| Property value | **MLS** (when sale active) > **county recorder** (when transfer recent) > **assessor** (always, but stale in CA) | |
| Mortgage amount | **County recorder mortgage filings** + **PACER Schedule D** | |
| Liens | **County recorder lien filings** + **CDIAC Mello-Roos** (CA) | |
| Bankruptcy | **PACER** | Only source |
| Vacancy / status | **City vacant registries** (parcel, deterministic) + **HUD-USPS** (tract, deterministic) + **code violations** (parcel, derived) + **BatchData** (parcel, paid) | Composite is required for full coverage |
| Property characteristics | **MLS** > **county assessor** | Assessor is universal but less detail than MLS |
| Last sale | **County recorder** (in disclosure states) + **MLS** | |
| Tax delinquency | **County tax collector / treasurer** | |
| Auction date | **Tax-sale platforms** (Bid4Assets / GovEase / RealAuction) + **sheriff sale listings** + **NOTS recordings** | |

### Where the data is sparsest (the gaps to plan around)

1. **Contact info (phone/email)** — virtually no free source. The only Tier 1 free sources are PACER bankruptcy debtor info ($0.10/page gated) and city vacant registries (where owner provided contact at registration). Realistic path: BatchData skip-trace at $0.10-0.20/lookup as opt-in per-record cost, or punt to "Find on Google" links per the TT pattern.

2. **Property value at parcel level for non-disclosure-state TX** — no free path. HCAD appraised value is the only proxy.

3. **Property value at parcel level for CA Prop 13 properties** — assessor is structurally stale. Must derive AVM from recent comps + FHFA HPI; harder in LA/SD/Riv/SB where recorder is paywalled.

4. **Liens beyond mortgages and tax** — mechanic's liens, judgment liens, IRS liens are recorded but there's no consolidated public lien registry. Each lien is filed separately at the county recorder. Coverage depends on recorder access.

5. **Property condition** — no public source carries "condition" as a field. Best proxies: code violations (deterministic for "bad"), MLS (when listed, with photos), aerial imagery (overgrown lawn detection), permit history (renovations).

### Implication for the property card

Putting this all together, a single property card needs to merge **at minimum 7 sources** to look complete:

1. County assessor roll → address, owner, mailing, value, chars, last sale
2. County recorder → mortgage, liens, deed transfers, NOD/NOTS
3. County tax collector → delinquency
4. City code violations → status proxy
5. City vacant registry → status confirmation
6. Tax-sale platform → auction date + min bid (when applicable)
7. Skip-trace (paid or "Find on Google" link) → contact

**That's the join graph.** Architecture must support efficient many-to-one joins from signals to the parcel backbone, with provenance preserved per signal so the property card can show "tax delinquency $5,200 from Cuyahoga Treasurer, observed 2026-04-15" rather than a soup of merged fields without sources.

### What this matrix recommends adding to the architecture

1. **Make the assessor roll's annual refresh a first-class concern** — it's the universal join key. Stale assessor data corrupts every other signal's interpretation.
2. **Plan for non-disclosure-state value-degradation in TX** — every Harris property card needs a "value: appraised by HCAD (no recorded sale price for TX)" disclaimer.
3. **Plan for CA Prop 13 stale-value handling** — derive AVM where possible; flag uncertain values when only assessor is available.
4. **Treat skip-trace as opt-in per-record paid action**, not a bundled feature. Property card has a "skip-trace this owner ($0.20)" button rather than auto-spending.
5. **Surface "fields populated by source" badges** on each property card — gives investors confidence in what they're looking at and what's a gap.
