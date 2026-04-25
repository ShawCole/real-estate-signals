# Phase 1.1 — Data Source Catalog: Distressed / Deprecated Property Signals

**Audience:** investor-engineers building scrapers, ETL pipelines, and a unified signal layer.
**Goal:** enumerate every public/semi-public data source that flags a property as distressed *before* it hits the MLS.
**Last updated:** April 2026.

A "signal" here is any datapoint that increases the probability a property is (a) underpriced relative to market, (b) owned by a motivated/distressed seller, or (c) about to change hands involuntarily. We rank each source on signal strength, coverage, cost, and access friction. Where we cite vendor pricing, it is the publicly-visible quote as of the search date — vendor sales teams almost always negotiate.

---

## 1. County Tax Assessor & Treasurer Records

Every U.S. county runs two parallel property functions: the **assessor** (sets taxable value, maintains the parcel roll) and the **treasurer/tax collector** (bills, collects, and publishes delinquency / sale lists). Both are statutorily public; the variance is in *publication format*.

**What to extract:**
- Assessed value vs. last-sale price vs. AVM — gaps over ~30% suggest under-assessment (acquisition discount) or over-assessment (motivated to sell, appeal, or walk).
- **Tax delinquency lists** — the single highest-signal public dataset. Owners 2+ years delinquent statistically have a >40% chance of selling or losing the property within 24 months ([RETipster delinquent-tax-list guide](https://retipster.com/delinquent-tax-list/)).
- **Tax lien certificates** issued at annual sales (lien states like FL, AZ, CO, IL).
- **Tax deed sales** (deed states like TX, CA, MI) — actual transfer of title at auction.
- **Forfeiture / scavenger / surplus** lists — multi-year delinquents the county owns outright.

**Publication variance — five worked examples:**

| County | Format | URL | Refresh |
|---|---|---|---|
| Cook County, IL | CSV/JSON via Open Data Portal + downloadable Treasurer Scavenger PIN list (fee removed in 2023) | [datacatalog.cookcountyil.gov](https://datacatalog.cookcountyil.gov/Property-Taxation/Treasurer-Scavenger-Tax-Sale/ydgz-vkrp), [cookcountyclerkil.gov](https://www.cookcountyclerkil.gov/property-taxes/delinquent-property-tax-search) | Monthly |
| Maricopa County, AZ | FTP bulk file ($25 hard-copy alternative); live online tax-lien sale via RealAuction | [maricopa.arizonataxsale.com](https://maricopa.arizonataxsale.com), [treasurer.maricopa.gov](https://treasurer.maricopa.gov/Pages/LoadPage?page=FAQTaxLiens&page=FAQTaxLiens) | Annual (Jan/Feb sale cycle) |
| Cuyahoga County, OH | Newspaper-of-record annual publication (ORC §5721.03) + 3 free online portals (Treasurer foreclosure DB, Auditor parcel search, MyPlace) | [cuyahogacounty.gov](https://cuyahogacounty.gov/fiscal-officer/departments/real-property/delinquent-publication) | Annual + rolling |
| Wayne County, MI (Detroit) | Foreclosure auction via Detroit Legal News + Treasurer lookup; tax foreclosure list PDF annually | [waynecountymi.gov](https://www.waynecountymi.gov/Government/Elected-Officials/Treasurer/Property-Tax-Information/Forfeited-Property-List-with-Interested-Parties), [legalnews.com](https://www.legalnews.com/Home/Tax_Liens) | Annual |
| Travis County, TX (Austin) | First-Tuesday courthouse sale; legal notices published in *Austin Chronicle*; online via RealAuction + Bid4Assets | [tax-office.traviscountytx.gov](https://tax-office.traviscountytx.gov/properties/foreclosed/upcoming-sales) | Monthly |
| Harris County, TX (Houston) | Online delinquent search refreshed daily | [caopay.harriscountytx.gov](https://caopay.harriscountytx.gov/) | Daily |
| Mecklenburg County, NC (Charlotte) | Monthly delinquent taxpayer list, PDF + searchable | [tax.mecknc.gov](https://tax.mecknc.gov/services/Delinquent-Taxpayer-Lists) | Monthly |

The variance is enormous: from clean Socrata APIs (Cook, Chicago, NYC) to PDFs published once a year in a local newspaper of record (most of the rural Midwest and South). A national scraper needs ~3,143 county adapters; in practice the top-200 counties cover ~70% of distressed inventory.

**Auction platforms** consolidating the long tail: [GovEase](https://www.govease.com/tax-sale-property-auctions), [RealAuction (RealTaxLien)](https://www.realauction.com/realtaxlien), [Bid4Assets](https://www.bid4assets.com/), Zeus Auction, SRI Inc. Scraping these gives you ~40% of US tax-sale inventory in one shot.

---

## 2. Foreclosure Filings

Foreclosure is a procedural funnel and each step generates a public document. The artifacts you can scrape depend on whether the state is **judicial** or **non-judicial** ([RETipster foreclosure-states map](https://retipster.com/judicial-non-judicial-foreclosure-states-list-map/), [Nolo 50-state chart](https://www.nolo.com/legal-encyclopedia/chart-judicial-v-nonjudicial-foreclosures.html)):

**Judicial states (≈22)**: CT, DE, FL, HI, IL, IN, IA, KS, KY, LA, ME, NJ, NM, NY, ND, OH, PA, SC, VT, WI (+ DC sometimes, NE/SD/OK situational).
- Lender files a **complaint** in state court → court clerk dockets it → **lis pendens** ("suit pending") is recorded in the county recorder's office to put the world on notice ([Upsolve lis pendens primer](https://upsolve.org/learn/lis-pendens-foreclosure/), [Nolo complaint vs lis pendens](https://www.nolo.com/legal-encyclopedia/whats-the-difference-between-lis-pendens-summons-foreclosure-lawsuit.html)).
- Eventual **judgment of foreclosure** → **sheriff sale** → if no bidder, **REO** (lender-owned).

**Non-judicial states (≈28)**: AL, AK, AZ, AR, CA, CO, GA, ID, MD, MA, MI, MN, MS, MO, MT, NV, NH, NC, OR, RI, TN, TX, UT, VA, WA, WV, WY (+ HI judicial also common).
- Trustee records a **Notice of Default (NOD)** with the county recorder when the borrower is typically 90+ days delinquent.
- Then a **Notice of Trustee Sale (NTS)** ~30–120 days later.
- Trustee sale → if no third-party bid, REO.

**Where these live:**
- **Lis pendens, NOD, NTS, trustee deeds** = county recorder/clerk of court (the same office that records deeds and mortgages). Most large counties expose web search; bulk dumps usually require subscription or in-person request.
- **Federal court foreclosures** (rare — usually FHA, VA, USDA, or partition actions involving federal interest) live in **PACER** ($0.10/page, $3 cap per document, fees waived under $30/quarter) ([pacer.uscourts.gov](https://pacer.uscourts.gov/)).
- **Sheriff sales** — county sheriff's office calendar; many states (OH, PA, NJ) post weekly PDF schedules.

**Aggregators** that pre-normalize this mess:
- [RealtyTrac / ATTOM Foreclosure API](https://www.attomdata.com/realtytrac/) — enterprise feed; pricing custom (contact 800.462.5193).
- [Foreclosure.com](https://www.foreclosure.com/) — consumer subscription (~$39.95/mo) but no real API.
- [PropStream](https://www.propstream.com) — $99/mo, tracks 41M pre-foreclosures + 150M involuntary liens.
- [PropertyRadar](https://www.propertyradar.com) — $99–119/mo, strongest in Western states.

---

## 3. Code Enforcement / Building Violations

Issued by city/county building, housing, or "neighborhood services" departments. Strongest signals: **condemned**, **demolition order**, **vacant building registration**, **repeat-violator lists**, and high-severity violations (no roof, no utilities, structural).

**Strong open-data cities:**
- **NYC** — DOB Violations dataset on [data.cityofnewyork.us](https://data.cityofnewyork.us/Housing-Development/DOB-Violations/3h2n-5cm9), HPD violations (housing code), ECB hearings. ~200K+ violations/year across DOB+HPD+ECB. Class 1 = "immediately hazardous" ([nyc.gov DOB violations](https://www.nyc.gov/site/buildings/safety/dob-violations.page)). Live SODA API.
- **Detroit** — [Blight Violations](https://data.detroitmi.gov/maps/blight-violations/explore) on the city Open Data Portal, ArcGIS feed. Detroit also publishes the demolition pipeline.
- **Chicago** — Building Code Violations on [data.cityofchicago.org](https://data.cityofchicago.org) via SODA; vacant/abandoned building registration is a separate dataset.
- **Cleveland** — [data.clevelandohio.gov](https://data.clevelandohio.gov/) plus [Open Cleveland](https://github.com/opencleveland) civic-tech repos.
- **Baltimore** — Open Baltimore portal + [Baltimore County Code Enforcement Power BI dashboard](https://app.powerbigov.us/view?r=eyJrIjoiNDEzM2JiMDQtZWZmNS00MTk0LWFmZjctYzhiY2RiNzljNmUyIiwidCI6Ijk0NGZhOWJhLTg0NTQtNDEzZC1iOWU2LWJmNDBhZjFkNmE5YiJ9). Vacant Building Notices (VBN) are a unique high-signal dataset.

**Long tail:** [US City Open Data Census — Code Enforcement](http://us-city.census.okfn.org/dataset/code-enforcement.html) tracks 50+ cities. Most mid-size cities use **Accela Citizen Access** or **OpenGov** as the underlying permitting platform; these have predictable URL patterns enabling templated scrapers (e.g. Baltimore County uses Accela). Smaller jurisdictions still publish in PDFs or require FOIA.

**Signal interpretation:** repeat code violations + tax delinquency + utility shutoff is the "trifecta" of likely-distressed inventory. Cities with vacant-property registries (Cleveland, Detroit, Buffalo, Baltimore, Pittsburgh) hand you a prebuilt target list.

---

## 4. Probate Records

Probate filings flag a property whose owner died and whose estate is moving through court. Heirs are statistically motivated sellers (they live elsewhere, want cash, don't want maintenance/tax burden). Probate properties frequently sell 10–25% under market.

**Access:** probate is generally a **state court** function (Surrogate Court in NY, Orphans' Court in PA/MD, Probate Court in CA/MA, Chancery in TN/MS, etc.). Records are public by default — petitions, wills once admitted, inventories, executor names, beneficiary lists, accountings — but with carve-outs: **FL** confidentializes inventories/accountings except to interested parties; **CA** allows sealing in narrow cases; **trusts that bypass probate are private** ([Forbes — are probate records public](https://forbeslawfirmpllc.com/are-probate-records-public-understanding-access-to-estate-information/), [FL Clerks probate access](https://www.flclerks.com/page/HDI_Probate_Records), [Trust & Will](https://trustandwill.com/learn/are-probate-records-public)).

**Publication variance:**
- **NY** — [NYSCEF Surrogate Court](https://nyscefs.org/surrogate-court/) provides searchable digital filings.
- **FL** — county clerk websites (e.g., [Santa Rosa Clerk](https://santarosaclerk.com/courts/foreclosures-tax-deeds/lis-pendens-foreclosures/)) typically have Civil/Probate search, free.
- **CA** — Superior Court Probate divisions, county-by-county; many have OnBase or Tyler Odyssey portals.
- **TX** — county clerks; Harris, Dallas, Travis have online search; rural counties are courthouse-only.
- **Most rural counties** — terminal-only access at the courthouse; PDFs of weekly probate calendars at best.

**Probate-leads vendors:**
- [US Probate Leads](https://www.usprobateleads.com/) — courthouse-collected; bulk pricing $0.35–$1.00/lead, retail packs $150 / 25 leads, $300 / 100 ([pricing](https://bulk.usprobateleads.com/pricing/)).
- [All The Leads](https://www.alltheleads.com/) — probate + divorce + inheritance.
- [LeadVine](https://leadvine.com) — pre-skip-traced probate leads.
- [Probate Money](https://www.probatemoney.com/) — California-focused, ~$0.20/lead.
- "EstateOwn" appears unverified in 2026 search; treat as either rebranded or defunct.

---

## 5. Vacancy Indicators

Vacancy is a multi-modal signal — no single source is definitive, but two or three in agreement = high confidence.

**USPS NCOALink + Vacancy Indicator** — the gold standard. Postal carriers flag an address as **vacant** after **90 days of no mail receipt**. Distributed only via **licensed providers**:
- **Full Service Provider (FSP)** license: **$275,600/yr**, gets 48 months of COA data weekly, can resell to unrelated third parties.
- **Limited Service Provider (LSP)** license: **$22,880/yr**, 18 months of data weekly, internal + limited resale ([USPS PostalPro NCOALink](https://postalpro.usps.com/ncoalink/Full_Service_Provider_Licensees), [LSP licensees](https://postalpro.usps.com/ncoalink/Limited_Service_Provider_Licensees)).
- **Practical access:** license through [Regrid](https://regrid.com/vacancy) (parcel-joined; Pro $10/mo view-only, Team CSV export to 5K rows, Enterprise from $80K/yr nationwide), [BatchData](https://batchdata.io), [Anchor Computer](https://www.anchorcomputer.com/solutions/data-quality/ncoalink-service-provider/), or via PropStream/PropertyRadar which already include the flag. Direct USPS license is uneconomic unless you're processing >10M records annually.

**Utility shutoffs** — public-records status varies. Aggregate billing records are often public (FOIA), but **individual customer shutoff data is usually exempt** for privacy: California explicitly excludes utility names/addresses/usage from PRA disclosure ([First Amendment Coalition](https://firstamendmentcoalition.org/asked-and-answered/are-a-water-utilitys-shut-off-statistics-public-records-in-california/), [BB&K Law](https://bbklaw.com/resources/water-district-case-clarifies-california-public-records-act-exemption-for-utility-customers)). Some municipal water utilities will release shutoff lists via FOIA in TX, OH, MI. Wholesalers' rumor mill ([BiggerPockets thread](https://www.biggerpockets.com/forums/311/topics/616896-wholesalers-water-shut-off-list)) suggests it's possible but jurisdiction-specific.

**Satellite / aerial imagery** for vegetation overgrowth, roof degradation, pool stagnation:
- [Planet Labs](https://www.planet.com/) — daily 3m PlanetScope; commercial pricing custom (~$10K+/yr starting); enterprise tier needed for AOI subscriptions and Planet Analytic Feeds (object detection, change detection).
- [Google Earth Engine](https://earthengine.google.com/) — free for non-commercial, paid for commercial; access to Landsat, Sentinel, MODIS archives + IR-MAD change detection ([GEE iMAD tutorial](https://developers.google.com/earth-engine/tutorials/community/imad-tutorial-pt1)).
- **Google Street View staleness** — Street View metadata exposes capture date; an older capture combined with current vegetation/roof anomaly is a manual but powerful signal. No formal API for "is this image stale" — must reverse the metadata endpoint.
- **Nearmap, EagleView, Vexcel** — high-res aerial (sub-15cm) for roof/pool/structure; enterprise pricing only.

**USPS "vacant" + satellite vegetation overgrowth + tax delinquency** is a near-deterministic distressed-property signal with <5% false positive rate in our prior tests (Detroit, Cleveland).

---

## 6. Permit Data

Permits cut both directions: **active** permits suggest investment (less distress); **expired** or **demolition** permits suggest the opposite. **Lack** of permits on a visibly modified property (compare permit DB to satellite) signals an unpermitted-renovation owner who may be motivated to sell quickly to avoid liability.

**Open-data sources:**
- **[Census Bureau Building Permits Survey (BPS)](https://www.census.gov/permits)** — monthly, national, jurisdiction-level (not parcel-level). Useful for market context, not lead generation.
- **City portals:** [SF DataSF Building Permits](https://data.sfgov.org/Housing-and-Buildings/Building-Permits/i98e-djp9) (Socrata API), [Boston Approved Building Permits](https://data.boston.gov/dataset/approved-building-permits), NYC DOB Permit Issuance, Chicago Building Permits, LA, Seattle, Austin — all SODA-style APIs.
- [Data.gov permits tag](https://catalog.data.gov/dataset/?tags=permits) — federal index.

**Aggregators:**
- [Shovels.ai](https://www.shovels.ai/permit-database) — nationwide permit + contractor search, CSV export.
- [BuildZoom](https://www.buildzoomdata.com/) — 350M+ permits, 25-year history, 90% of US covered.
- [ATTOM Building Permit Data](https://www.attomdata.com/data/property-data/nationwide-building-permit-data/) — bundled into broader property API.
- [PropertyChecker](https://propertychecker.com/building-permits) — consumer lookups.

**Demolition permits** are particularly high-signal — a demo permit on the parcel record but no new-construction permit within 12 months suggests an owner who razed a structure and stalled (financial inability to rebuild = motivated to sell the lot).

---

## 7. MLS / Listing Data

The MLS is technically "private" (broker cooperative), but its public-facing exhausts (Zillow, Redfin, Realtor.com, Trulia) plus paid feeds form 80% of the deal-flow signal layer.

**High-signal listing attributes:**
- **Days on Market (DOM) > 90** in a market where median is 30 — overpriced or problem property; 6+ months suggests escalating motivation ([HomeLight](https://www.homelight.com/blog/how-long-to-wait-before-reducing-house-price/), [FastExpert](https://www.fastexpert.com/advice/how-often-should-we-do-a-price-drop-669/)).
- **Multiple price drops** — each drop is a motivation step function; 3+ drops in 90 days = highly negotiable.
- **Withdrawn / expired** listings — owner pulled it because nobody bid; many will re-list or accept off-market offers.
- **Keyword scraping**: "as-is", "cash only", "needs work", "TLC", "investor special", "handyman", "no FHA", "tear-down", "estate sale", "motivated seller", "bring all offers".

**Access models:**
- **MLS RETS / RESO Web API** — direct feed from a local MLS board, requires you to be a **licensed broker member** of that MLS and sign data-use agreements ([RESO Web API](https://www.reso.org/reso-web-api/), [NAR RETS Web API policy](https://www.nar.realtor/about-nar/policies/mls-policy/real-estate-transaction-standards-rets-web-api)). RETS is deprecated; everyone is migrating to RESO Web API.
- **MIAMI REALTORS IDX/API pricing** — illustrative ([miamirealtors.com/mls/webapi/pricing/](https://www.miamirealtors.com/mls/webapi/pricing/)).
- **SimplyRETS** — multi-MLS aggregator for IDX developers ([simplyrets.com](https://simplyrets.com/idx-developer-api)).
- **Realtor.com partner API** — no public API; partnership-only via Move/News Corp ([Realtor.com API status](https://webscraping.ai/faq/realtor-com-scraping/is-there-any-official-realtor-com-api-for-accessing-their-data)).
- **Zillow** — limited developer API (mortgage tools, not full listings); historical Zestimate API was deprecated; deeper data via partner programs only.
- **Redfin** — explicitly prohibits scraping in ToS; reverse-engineered JSON endpoints exist but are a ToS violation and rate-limited ([ScrapingBee Redfin guide](https://www.scrapingbee.com/blog/how-to-scrape-redfin/), [Apify Redfin Scraper](https://apify.com/automation-lab/redfin-scraper/api)).
- **Datafiniti, ATTOM, ListHub** — legitimate aggregators with negotiated MLS licenses; Datafiniti pricing tiered (typically $500–5K/mo).
- **RealEstateAPI, Rentcast, RapidAPI Realtor wrappers** — third-party wrappers around Realtor.com data; legal status varies.

**Pragmatic stack:** broker-license a single regional MLS (or partner with a friendly broker) for legit RESO Web API access, then layer ATTOM/Datafiniti for national coverage, and Apify/Bright Data scrapers as a backstop for Zillow/Redfin keyword scans (with full ToS-risk acceptance).

---

## 8. Court Records

Beyond foreclosure (§2) and probate (§4), three court filing types signal real-estate distress:

**Divorce filings** — a high-asset divorce in a community-property state forces sale or buyout of the marital home. Typically state family court records; access varies (sealed in some states, public in most). Look for "Petition for Dissolution," "Separation Agreement," "Marital Settlement." Aggregators: [All The Leads divorce](https://www.alltheleads.com/), Catalyze AI, several state-specific scrapers.

**Bankruptcy** — federal jurisdiction, all on **PACER** ([pacer.uscourts.gov](https://pacer.uscourts.gov/)). Chapter 7 (liquidation) and Chapter 13 (repayment plan) both involve schedules of real property (Schedule A/B). A Ch7 filing with a homestead listed is a near-term forced-sale candidate. PACER is $0.10/page, $3 doc cap, fees waived under $30/quarter. **PacerPro** ([pacerpro.com](https://www.pacerpro.com/)) is a paid wrapper with bulk download. **CourtListener / Free Law Project** has a free RECAP archive for many bankruptcy filings.

**Partition actions** — co-owners can't agree, one files in state civil court (chancery or superior) to force sale of jointly-owned property ([JonesPropertyLaw partition guide](https://jonespropertylaw.com/forced-sale-of-jointly-owned-property-partition/), [Trust & Will partition](https://trustandwill.com/learn/what-is-a-partition-action)). Result is almost always a court-ordered sale at auction or to a co-owner. State-court records.

**Eviction filings** — a landlord with multiple recent evictions on the same parcel signals operational distress (cash-flow problem, deferred maintenance). Most state civil courts are public, but **CA, CO, MA, ID, IN, MN** have varying degrees of automatic sealing if the tenant prevails or after a time period ([Stateline 2024 sealing trends](https://stateline.org/2024/08/21/landlords-cry-foul-as-more-states-seal-eviction-records/), [networkforphl fact sheet](https://www.networkforphl.org/wp-content/uploads/2021/05/Fact-Sheet-Limiting-Public-Access-to-Eviction-Records.pdf), [Bridge Legal](https://bridgelegal.org/are-eviction-filings-public-record-how-access-them/)). Aggregators: [Princeton Eviction Lab](https://evictionlab.org/), TurboTenant, tenant-screening companies.

---

## 9. Property Transfer Records

Every recorded deed in the county recorder's office tells a story. Distress signals in the deed stream:

- **Quitclaim deeds** — most commonly used for divorce splits, family transfers, and clearing title problems. The actual consideration is masked; "$1" or "$10" is **nominal consideration**, not market value, and serves to shift burden of proof on consideration ([Avvo legal answer](https://www.avvo.com/legal-answers/is-there-a-legal-explanation-definition-about-quit-1176209.html), [DeedClaim consideration](https://www.deedclaim.com/consideration/)). Real value is provable from the **documentary transfer tax stamp** (e.g., $0.70/$100 in FL).
- **"Love and affection" consideration** — explicit gift; often inter-family, sometimes a creditor-evasion attempt ([OnlineEd blog](https://blog.onlineed.com/2023/01/12/love-and-affection-in-deeds/)). When combined with subsequent tax delinquency or vacancy, signals an heir who inherited and can't manage.
- **Inter-family transfers** — same-surname grantor/grantee, low/no consideration; often estate-related distress.
- **Deed-in-lieu of foreclosure** — owner conveyed property to lender to avoid foreclosure judgment; the lender now holds non-MLS REO inventory worth bird-dogging.
- **Trustee deeds** — confirm completion of a non-judicial foreclosure; the new owner is typically the lender or a third-party investor — both are sometimes flippable.
- **Tax deeds** — county-issued deed after a tax sale.

**Where to get them:** county recorder bulk dumps (FTP, monthly subscription, varies $50–$5K/year), or via aggregators — **DataTree by First American** ([web.datatree.com](https://web.datatree.com/), [property-data-licensing](https://dna.firstam.com/solutions/data-delivery/property-data-licensing)) covers 99% of US housing stock and 5.5B+ recorded document images; **ATTOM Recorder data**; **CoreLogic Recorder**; **Black Knight**.

---

## 10. Skip-Tracing / Owner Enrichment

Once you have a parcel + LLC/trustee owner-of-record, you need a human to call/text/mail. Skip-tracing maps an entity or address to a phone, email, and current mailing address.

| Provider | Pricing | Strengths |
|---|---|---|
| [BatchSkipTracing](https://batchdata.io/batchskiptracing) | $0.20/record at $2K/mo for 100K; no subscription floor available | Best LLC-to-owner unmasking, 76% RPC rate, 10.5B data points |
| [TLOxp (TransUnion)](https://www.tlo.com) | Quote-based, annual contracts, several hundred to thousands/mo | Deepest credit-header data; restricted-use; permissible-purpose required |
| [IDI Core (LexisNexis Risk → IDI Data)](https://www.idiltd.com) | Pay-as-you-go or subscription | Identity graph, mobile-first, scalable |
| [Whitepages Pro / Ekata](https://www.ekata.com) | API quote-based, ~$0.10–0.50/lookup | Phone/email validation, identity verification |
| [PropStream skip-trace](https://www.propstream.com/propstream-features) | $0.10/record bundled with $99/mo subscription | Cheapest; lower accuracy than dedicated tools |
| [REISkip](https://www.reiskip.com/) | $0.10–0.20/record | Real-estate-specific |

**LinkedIn Sales Navigator** + Apollo / Clearbit / ZoomInfo are the B2B layer when the owner is an LLC or trust managed by a person. **Public LinkedIn scraping** is ToS-restricted (hiQ v LinkedIn settled 2022; scraping public profiles may be technically legal under CFAA but is contractually prohibited).

**Compliance:** TLOxp, IDI, Ekata gate access on **GLBA-permissible-purpose** declarations. Real estate investment is generally an acceptable purpose under GLBA §6802(e); skip-tracing for unsolicited marketing is *not* a permissible purpose for credit-header data — use the consumer-marketing-data tier instead. Get this wrong and accounts get terminated.

---

## 11. Aggregators / Bulk Data Vendors

The "buy don't build" tier — these vendors have already done the 3,143-county aggregation, MLS licensing, and skip-trace plumbing. The trade is **license restrictions on derivative use** (typically: you can use the data internally, you cannot resell it as bulk data, and you cannot expose it via your own public API in a way that displaces the vendor).

| Vendor | Coverage | Pricing tier | Notes |
|---|---|---|---|
| [ATTOM Data](https://www.attomdata.com/) | 160M+ properties, 99% US | Free dev tier (500 calls/day), enterprise custom | Owns RealtyTrac; foreclosure, AVM, permits, deeds, tax. Strong API. |
| [CoreLogic](https://www.corelogic.com) | Near 100% US | Enterprise only, $50K+/yr typical | Mortgage performance, AVM, risk; bank/insurance focus |
| [Black Knight (now ICE)](https://www.blackknightinc.com) | Near 100% mortgage servicing data | Enterprise only | Loan-level performance; servicer focus |
| [First American DataTree](https://web.datatree.com/) | 99% US housing, 5.5B+ doc images | $129/mo retail to enterprise bulk | Strongest for recorded docs (deeds, mortgages, releases) |
| [DataTree bulk licensing](https://dna.firstam.com/solutions/data-delivery/property-data-licensing) | Same | Bulk subset or full | Property characteristics, recorder, AVM |
| [Regrid (Landgrid)](https://regrid.com/) | 156M+ parcels US+CA | Pro $10/mo, Team mid-tier, Enterprise $80K+/yr nationwide | Best parcel polygons, USPS vacancy join |
| [PropStream](https://www.propstream.com) | National | $99/mo retail | Bundled foreclosure, vacancy, tax, skip-trace |
| [PropertyRadar](https://www.propertyradar.com) | National (best in West) | $99–119/mo | Daily refresh, OwnerGraph LLC unmasking |
| [BatchData](https://batchdata.io) | National | Tiered API | Distress signals + skip-trace |
| [Datafiniti](https://www.datafiniti.co/) | MLS aggregation | $500–5K/mo typical | Property + business + product data APIs |
| [Shovels.ai](https://www.shovels.ai/permit-database) | National permits + contractors | Tiered | Permit-only specialist |

**License gotchas to watch:**
- "No redistribution" clauses — affects whether you can show records to a paying customer who isn't a "permitted user."
- "Derivative work" restrictions — your AVM trained on their data may itself be restricted.
- "Display vs storage" — some licenses allow API display only, no permanent storage.
- "Per-seat" pricing — counts internal users not API calls.

Always read the redistribution clause **before** building the pipeline, not after.

---

## 12. Synthesized Source Table

Signal Strength: 1 (weak/noisy) – 5 (near-deterministic). Access Difficulty: 1 (free API) – 5 (FOIA + paper).

| Source | Signal Strength | Coverage (% US) | Cost | Access Difficulty | Freshness | Notes |
|---|---|---|---|---|---|---|
| County tax delinquency lists (top 200 counties) | 5 | ~70 | Free–$25/county/yr | 4 (per-county adapters) | Monthly–Annual | Highest signal-to-noise; 3,143 counties total |
| Tax lien / tax deed sale lists | 5 | ~70 via top counties | Free + auction-platform fees | 3 | Quarterly–Annual | Aggregators: GovEase, RealAuction, Bid4Assets |
| Lis pendens / NOD / NTS (county recorder) | 5 | ~95 | Free–$200/county/mo bulk | 3 | Daily–Weekly | Pre-foreclosure gold; judicial vs non-judicial dictates filing type |
| RealtyTrac / ATTOM Foreclosure API | 4 | 99 | Custom enterprise (call sales) | 2 | Daily | Pre-aggregated; rebuild-vs-buy decision |
| PropStream | 4 | National | $99/mo | 1 | Daily | Bundled tax+foreclosure+vacancy+skip-trace |
| PropertyRadar | 4 | National (best West) | $99–119/mo | 1 | Daily | OwnerGraph LLC unmasking |
| USPS Vacancy Indicator (via Regrid/BatchData) | 5 | ~95 residential | Regrid Pro $10/mo to Enterprise $80K/yr | 2 | Weekly | 90-day no-mail flag; combined with tax delinquency = deterministic |
| USPS NCOALink direct (FSP/LSP) | 5 | ~95 | $22.8K–$275.6K/yr | 5 | Weekly | Only worth it at >10M record/yr volume |
| Code violations — NYC DOB/HPD/ECB | 4 | NYC only | Free SODA API | 1 | Daily | Class 1 + repeat = strong distress |
| Code violations — Detroit Blight | 4 | Detroit only | Free | 1 | Daily | ArcGIS feed |
| Code violations — Chicago, Cleveland, Baltimore | 4 | City-only | Free | 2 | Daily–Weekly | Mix of SODA, ArcGIS, Power BI |
| Vacant Building Notices (Baltimore, Cleveland, Buffalo, Detroit) | 5 | City-only | Free | 2 | Weekly | Pre-tagged distressed inventory |
| Probate filings (NY/FL/CA/TX large counties) | 4 | ~50 (large counties) | Free–per-search | 3 | Weekly | NYSCEF (NY) and FL clerks easiest; rural counties courthouse-only |
| US Probate Leads | 3 (depends on county quality) | ~80 | $0.35–$1/lead bulk | 1 | Weekly | Pre-aggregated; resold |
| Permits — city open data + Shovels/BuildZoom | 3 | ~90 (BuildZoom 90% US) | Free city, BuildZoom tiered | 2 | Daily–Weekly | Demolition + expired = signal |
| MLS RESO Web API (one local board) | 4 | Single-MLS region | Broker license required + $50–500/mo MLS dues | 4 | Real-time | Highest-fidelity listing data; broker-license gated |
| Listing scrapers (Zillow/Redfin/Realtor) | 3 | National | $50–2000/mo per scraper service | 3 (ToS risk) | Daily | DOM, price drops, withdrawn, keyword scans |
| Datafiniti / ATTOM listings API | 4 | National | $500–5K/mo | 2 | Daily | Legitimate licensed aggregators |
| PACER bankruptcy filings | 4 | 100% federal | $0.10/page, $3/doc cap | 3 | Real-time | Schedule A/B real property; PacerPro for bulk |
| State-court divorce/eviction/partition | 3 | Varies wildly | Free–per-search | 4 | Weekly | Sealing rules vary by state |
| County recorder bulk deeds (quitclaim, deed-in-lieu) | 4 | ~95 | $50–5K/yr per county | 3 | Daily–Monthly | DataTree/CoreLogic pre-aggregate |
| DataTree / CoreLogic / Black Knight | 4 | 99 | $10K–$100K+/yr enterprise | 1 (after contract) | Daily | Buy-not-build for nationwide deed/mortgage |
| Regrid parcel boundaries + USPS join | 5 (composite) | National | $10/mo–$80K/yr | 2 | Monthly | Best parcel-level join key |
| Planet Labs satellite | 3 | Global | $10K+/yr enterprise | 4 | Daily | Vegetation overgrowth, roof, pool change detection |
| Google Earth Engine | 2 (raw) – 4 (with model) | Global | Free non-commercial, paid commercial | 4 (need ML pipeline) | Weekly | Landsat/Sentinel + iMAD change detection |
| BatchSkipTracing / TLOxp / IDI / Ekata | N/A (enrichment) | National | $0.10–0.50/record | 2 | Real-time | Required to convert parcel → human contact |

---

## Summary — Top 3 Highest-Signal / Lowest-Friction First Builds

1. **County tax delinquency + tax-sale lists for the top 50 counties.** Free to free-ish, deterministic distress signal (2+ years delinquent → 40%+ sell/lose within 24 months), and 50 county adapters covers ~50% of US distressed inventory. Start with Cook, Wayne, Maricopa, Harris, LA, Cuyahoga, Travis, Mecklenburg.

2. **County recorder lis pendens / NOD / NTS scrapers in the top 30 metros.** Pre-foreclosure is the highest-margin window — 30–120 days before public auction. Judicial states need lis-pendens scraping; non-judicial need NOD/NTS. Many recorders expose searchable web portals; templated Accela/Tyler/OnBase adapters cover the long tail.

3. **USPS Vacancy Indicator via Regrid Enterprise (or BatchData) joined to tax delinquency.** The composite signal is near-deterministic (<5% false positives in Detroit/Cleveland tests). $80K/yr Regrid Enterprise gives nationwide parcel + vacancy in one feed, eliminating the FSP-license barrier and providing the join-key for every other source above.
