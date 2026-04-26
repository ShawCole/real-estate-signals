# Value-Bearing Signals — Which Sources Tell Us What a Property Is Worth

**Why this matters:** distress without value is half a deal. A property in tax delinquency at $50K is a wholesale flip; the same delinquency at $5M is a probate estate sale. The value column is what makes a signal actionable, not just interesting.

Every cataloged source falls into one of three tiers based on whether it carries value information. The architecture must lean on Tier-1 sources for value-of-record, with the assessor roll as the universal join key for Tier-2 signals.

---

## Tier 1 — Value embedded in the signal itself

These sources publish a dollar value as part of the record. No join required to know what the property is worth.

| Signal | Value type carried | Coverage | Notes |
|---|---|---|---|
| **County assessor roll / parcel data** | **Assessed value** + land/improvement split. Many counties also publish "market value" or "appraised value" estimates separately. | All MVP-5 + all CA counties + most US | Backbone. The mandatory join key. |
| **Recorded deed transfers** (disclosure states) | **Actual sale price** at the moment of transfer | Cuyahoga OH, Philly PA, Cook IL, Maricopa AZ, all CA, FL, NC, GA, etc. | Most accurate — actual cash changed hands |
| **Recorded deed transfers** (non-disclosure states: TX, AK, ID, KS, MS, MT, NM, ND, UT, WY) | ❌ Sale price NOT disclosed | **Harris TX (our MVP-5!)**, Travis TX, Dallas TX, all TX counties | Workaround: use county appraisal district's appraised value (HCAD does publish these). Less accurate but free. |
| **Mortgage recordings** | **Loan amount** (always disclosed at recording — federal disclosure law) | All states | Combined with deed sale price → derive LTV at acquisition |
| **Foreclosure filings** (NOD / NOTS / lis pendens) | **Outstanding loan balance** (often disclosed); sometimes default amount only | All states (where recorder is accessible) | Gap between balance and current value = underwater status; deterministic motivated-seller indicator |
| **Sheriff sale notices** | **Judgment amount + minimum bid** | Judicial-foreclosure states | The minimum bid is the cheapest you can possibly acquire it |
| **Tax sale auction lists** | **Delinquent tax amount + minimum bid + assessed value** | All states with tax-sale programs (essentially all 50) | Often published with full parcel context including assessed value |
| **HUD Home Store** | **List price** | National | Direct REO sale |
| **VA REO** | **List price** | National | |
| **USDA Rural Dev REO** | **List price** | National (rural) | |
| **GSA Auctions** | **Bid price** (live + historical) | Federal property | |
| **Treasury Forfeiture** | **Bid price** | National | |
| **Bid4Assets / RealAuction / GovEase** | **Minimum bid + recent winning bid** + assessed value | County-by-county tax-sale platforms | All 3 publish full value context |
| **PACER bankruptcy Schedule A/B** | **Debtor's claimed value** of real property | Federal | $0.10/page so this is partially gated |
| **MLS RESO Web API** (one local broker license) | **List price + last-sale price + comps** | Single MLS region | Broker-license-gated; one license per region we cover |
| **CDIAC Mello-Roos CFD reports** (CA-only) | **Bond default amount + assessed value of CFD parcels** | CA statewide | Unique CA-only value-bearing signal |

---

## Tier 2 — Value via join to assessor roll

These signals reference a parcel but don't carry value themselves. To make them actionable, we join `signal.parcel_id → parcels.assessed_value` (and where available, `parcels.market_value`).

| Signal | Value source after join |
|---|---|
| **City code violations** (Socrata + ArcGIS + Accela) | parcels.assessed_value |
| **City vacant-property registries** (Cleveland VAPR, Chicago VBR, Detroit, Buffalo) | parcels.assessed_value |
| **Repeat-violation / dead-end / dangerous-buildings flags** (Pittsburgh, KCMO, Charlotte) | parcels.assessed_value |
| **Building permits** (renovation, demolition) | Permit declared cost (when listed) + parcels.assessed_value. Permit declared cost is itself sometimes a value signal (a $200K renovation permit on a $300K property = signal). |
| **Probate filings** (where filed online) | Estate inventory sometimes lists appraised value of real property, otherwise parcels.assessed_value |
| **Eviction filings** (Harris JP, SF, NYC HPD) | parcels.assessed_value (LL property, not tenant context) |
| **Recorded liens** (mechanic's, judgment, IRS, state tax) | Lien face amount (disclosed at recording) + parcels.assessed_value gives lien-to-value ratio |

---

## Tier 3 — No parcel-level value (context layers)

These don't tie to specific parcels — they're geographic, regulatory, or aggregate. They influence scoring as a context multiplier, not as a per-property value signal.

| Signal | Granularity |
|---|---|
| **HUD-USPS aggregated vacancy** | Census tract — no parcel value |
| **OpenFEMA disaster declarations** | County / ZIP polygon |
| **CalFire wildfire perimeters** | Polygon |
| **CDI insurance non-renewal** (CA) | ZIP code |
| **Census ACS** (housing characteristics, vacancy rates, median value) | Census tract — useful as **comparable context** for assessor values |
| **WARN Act mass layoffs** | Employer address + employee count |
| **Federal Register filings** | Regulatory, not parcel |
| **News RSS / press releases** | Variable |
| **FRED economic indicators** | Metro/county |
| **FHFA House Price Index** | MSA — useful as **calibration baseline** for AVM |
| **BLS unemployment** | Metro |

---

## The 3 architectural implications

### 1. The assessor roll is mandatory, not optional.

Every Tier-2 signal becomes useless without `parcels.assessed_value` to join against. **Day 1 of the MVP must ingest the assessor roll for all 5 MVP counties before any other signal scraper runs.** This was already in the architecture but worth re-emphasizing in the value context.

For our MVP-5 counties:
- Harris TX → HCAD bulk parcel + appraised value (annual; non-disclosure state means appraised value is the only value field we get)
- Cuyahoga OH → Cuyahoga MyPlace (parcel + assessed + market value)
- Philadelphia PA → OPA Property assessment (full disclosure)
- Cook IL → Cook County Assessor (full disclosure)
- Maricopa AZ → Maricopa Assessor (full disclosure)

### 2. Non-disclosure states are a known coverage gap.

**Texas** (our largest MVP county, Harris) is a non-disclosure state — recorded deeds don't show sale price. This means in Harris specifically:
- We have **appraised value** (HCAD's annual estimate, used for tax purposes)
- We do **NOT have** actual sale prices from deed transfers
- Property cards in Harris will show `appraised: $X` instead of `last sold: $X`
- This degrades our equity-position calculations (can't compute "owner has $Y in equity at current market") in TX

The other 9 non-disclosure states (AK, ID, KS, MS, MT, NM, ND, UT, WY) are not in our MVP-5 or expansion-30, so this is a Harris-only problem in the near term.

### 3. CA has its own value-data pathology: Prop 13.

In California, **assessor "assessed value" is structurally stale** — Prop 13 freezes assessed value at the last sale price (with limited annual escalation). A house bought in 1985 for $80K may be assessed at $130K today while market value is $1.2M. **The CA assessor roll cannot be used as a proxy for current market value.**

The workaround for CA:
- **Use recent comparable sales** (from the recorder, where available) to derive a per-parcel AVM
- **Pull HUD-FHFA House Price Index** to scale stale assessed values forward by neighborhood appreciation
- **For LA/SD/Riv/SB specifically** (where the recorder is paywalled), this is much harder — we'd need a paid AVM service ($0.05-0.10/lookup at ATTOM, or build our own slow AVM from public comps)

This is a structural reason we should prioritize the wide-open CA counties (SF/Alameda/OC/Sac) over LA/SD/Riv/SB — the value math actually works there.

### 4. The simple-AVM build is a near-term opportunity.

Once we have the assessor roll + recent recorded sale prices for all 5 MVP counties, we can build a **county-local AVM** (per-zip mean-price-per-sqft scaled by year, beds, baths) in ~1 engineering day. Free, simple, surprisingly competitive with paid AVMs at the 80th percentile of accuracy. Adds an `estimated_market_value` column to `parcels` that's more useful than raw `assessed_value` everywhere except Harris (non-disclosure → no transactions to feed the AVM) and CA-paywalled-counties (no recent sale price access).

---

## Updated property card

The dashboard property card should always show this stack of value fields, in this priority order:

1. **Recent sale price** (if recorded transfer in last 24 months — most accurate)
2. **Estimated market value** (our county-local AVM — for everything else)
3. **Assessed value** (county roll — fallback)
4. **Comparable sales** (3-5 recent neighborhood transactions of similar size)
5. **Equity estimate** (sale price or market value MINUS outstanding mortgage from recorded loan amount, decayed by paydown schedule)

For the foreclosure filing case specifically, the card should display:

- **List price / market value:** $X
- **Foreclosure outstanding balance:** $Y
- **Equity / shortfall:** $X - $Y
- **Tax delinquency:** $Z
- **Total claims:** $Y + $Z
- **Distress headroom:** $X - $Y - $Z (negative = underwater = motivated seller)

This is how investors actually think about deals. The platform that surfaces this composite first wins on UX.

---

## Cost note

All Tier-1 and Tier-2 value-bearing data is **free** in the counties where the recorder is accessible. The exceptions:

- **TX deed sale prices** — not published, no workaround free or paid (it doesn't exist)
- **LA/SD/Riv/SB recorder data** (CA) — paywalled by statute / vendor; ParcelQuest is $50-200/mo
- **MLS list prices** — broker-license-gated; one license per region we cover (~$5-10K/yr per region)
- **PACER bankruptcy** — $0.10/page

The free path covers everything we need for the 3 wide-open CA counties + 4 of the 5 MVP-5 counties. Harris TX has the appraised-value workaround. Conclusion: **value-bearing data is available free for ~80% of our planned coverage**, with paid workarounds available for the rest at low cost.
