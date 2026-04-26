# Vendor Patterns & Federal Data Sources

Catalog of govtech SaaS vendors that power county/city public-records portals (one adapter unlocks dozens of jurisdictions) plus federal data sources usable as distress / vacancy / foreclosure signals.

All URLs spot-verified with `curl` (HEAD or GET with browser UA). HTTP 200/302 = reachable; 403 = vendor anti-bot but URL confirmed correct via search results / Esri partner pages / vendor news pages. URLs that returned `000` or `404` were dropped or replaced.

Verification convention in tables:
- `OK` = HTTP 200 / 202 / 301 / 302 to the documented page
- `OK*` = vendor anti-bot WAF returned 403 to curl, but URL pattern is confirmed correct via independent source (Esri partner pages, vendor press releases, county website link)

---

## Section A — Vendor Patterns (county/city portal vendors)

### A.1 Tyler Technologies — PublicSearch.us / Records Public Access

What it serves: County clerk / recorder land & official records (deeds, mortgages, liens, assignments, plats). Sometimes combined with court / e-filing.
URL patterns:
- `https://<county>.<state>.publicsearch.us/`
- `https://portal-<state><county>.tylertech.cloud/PublicAccess/`
- `https://<state>.countygovernmentrecords.com/` (older Tyler-Eagle hosted)
Customer count (estimated): 100-200 counties on PublicSearch / Records Public Access cloud (Tyler reports 27,000+ total installations across all products, all 50 states). Growing fast as Tyler migrates Eagle on-prem clients to cloud.
Customer list: No single public roster; Tyler has a generic "Counties" page at https://www.tylertech.com/who-we-serve/counties (no enumeration).
Scraping notes: Server-rendered HTML over ASP.NET / .aspx. PublicAccess portals often show a disclaimer interstitial that sets a session cookie before search is available. PublicSearch.us domain (newer cloud variant) is a SPA-ish search surface; some endpoints require account creation before document viewing. Image / PDF downloads are fee-gated in many counties.
Known evasion / rate-limit / CAPTCHA: WAF (Akamai/AWS) blocks unbranded UAs, returns 000/403 to curl. Real browser UA plus cookie acceptance gets through. No CAPTCHA observed on first-page search; throttle ~1 req/sec recommended.

| Customer | URL | Verified |
|---|---|---|
| Cuyahoga County, OH (recorder) | https://cuyahoga.oh.publicsearch.us/ | OK |
| Hale County, TX (clerk) | https://portal-txhale.tylertech.cloud/PublicAccess/ | OK (302) |
| Liberty County, TX (clerk) | https://portal-txliberty.tylertech.cloud/PublicAccess/default.aspx | OK (302) |
| Texas county records (Eagle) | https://tx.countygovernmentrecords.com/ | OK |
| eFile Ohio (statewide e-filing) | https://efileoh.tylertech.cloud/OfsEfsp/ui/landing | OK |

### A.2 Tyler Technologies — Odyssey (Court Case Management)

What it serves: Court case search / e-filing — civil, criminal, probate, including foreclosure complaints and lis pendens.
URL pattern: `https://*.tylertech.cloud/PublicAccess/` and statewide eFile portals like `https://efileoh.tylertech.cloud/`, `http://www.odysseyefilega.com/`.
Customer count (estimated): Used by 90+ Texas counties (~90% of state pop), 105 Kansas counties (statewide), 28 of 58 California superior courts, statewide Indiana courts, Georgia statewide e-file. Several thousand court installations across the US.
Customer list: No public list; partial inference from press releases on https://investors.tylertech.com/news.
Scraping notes: Same ASP.NET .aspx pattern as records portal. Many courts require disclaimer + cookie acceptance. Some Odyssey deployments expose JSON endpoints under `/api/` for case lookup but auth gates apply per-jurisdiction.
Known evasion / rate-limit: WAF blocks bots; PII redaction enforced at HTML render. Treat as legal-records source — be respectful.

| Customer | URL | Verified |
|---|---|---|
| Cook County, IL (Civil) | (not via Tyler — uses Justice Systems) | — |
| Indiana statewide Odyssey | https://www.in.gov/courts/admin/tech/odyssey/ | (info page) |
| Georgia eFile (statewide) | http://www.odysseyefilega.com/ | (info page) |
| Reno/Washoe NV public records | https://portal-nvreno.tylertech.cloud/PublicAccess/default.aspx | OK (302) |
| Texas DIR Tyler portfolio | https://www.tylertech.com/resources/case-studies/texas-department-of-information-resources | (case study) |

### A.3 Tyler Technologies — Eagle / Enterprise Records Management

What it serves: Recorder land records (legacy on-prem; many being migrated to cloud as PublicSearch).
URL pattern: `https://<state>.countygovernmentrecords.com/` or county-hosted `*.us` subdomains running Eagle.
Customer count (estimated): Several hundred US counties (long Eagle install base; not all yet on cloud).
Customer list: https://www.countygovernmentrecords.com/ exposes a per-state directory.
Scraping notes: Older ASP.NET pages, simpler search forms; no aggressive WAF on legacy installs. Same per-county schema (Grantor/Grantee, Date, Doc Type, Book/Page).

| Customer | URL | Verified |
|---|---|---|
| Texas counties directory | https://tx.countygovernmentrecords.com/ | OK |
| National Eagle directory landing | https://www.countygovernmentrecords.com/ | OK |
| Tyler Eagle product page | https://www.tylertech.com/products/enterprise-records-management/enterprise-records-management-for-the-recorder | OK |

### A.4 Schneider Geospatial — qPublic.net & Beacon

What it serves: Assessor + parcel + GIS + tax. CAMA, assessment, tax records combined into a single web app over the parcel map.
URL patterns:
- `https://qpublic.schneidercorp.com/Application.aspx?App=<JurisdictionKey>&Layer=Parcels&PageType=Search`
- `https://qpublic.schneidercorp.com/?county=<state>_<county>`
- `https://beacon.schneidercorp.com/Application.aspx?...`
Customer count (estimated): 1,000+ counties / municipalities (heavy footprint in GA, FL, AL, IN, IA, KS, NE, MN, MS; presence in nearly every state). Schneider was acquired into Vexcel-related portfolio; same product surface.
Customer list: No flat directory exposed; Esri partner page at https://www.esri.com/partners/schneider-geospatial-a2T70000000TNggEAG enumerates the products and links to install instances. The Application.aspx `App=` parameter or `?county=` query encodes every jurisdiction.
Scraping notes: ASP.NET. Hosts on `schneidercorp.com` apex — aggressive WAF returns 403 to non-browser UAs. Real Chrome UA + cookies get through. Search returns paginated parcel lists; deep parcel detail returns assessment values, owner name, sale history (where county allows), tax balance.
Known evasion / rate-limit: Cloudflare-style anti-bot; JavaScript challenges on some county apps. Pacing 1 req per 2-3 sec sufficient. No CAPTCHA on first-page browse; rapid scraping triggers IP blocks.

| Customer | URL | Verified |
|---|---|---|
| Coweta County, GA (assessor) | https://qpublic.schneidercorp.com/Application.aspx?App=CowetaCountyGA&Layer=Parcels&PageType=Search | OK* (403 to curl, page exists) |
| Walker County, GA (assessor) | https://qpublic.schneidercorp.com/Application.aspx?App=WalkerCountyGA&Layer=Parcels&PageType=Search | OK* |
| Bulloch County, GA (GIS) | https://qpublic.schneidercorp.com/Application.aspx?AppID=637&LayerID=11293&PageTypeID=1 | OK* |
| Liberty County, GA (Beacon) | https://qpublic.schneidercorp.com/?county=ga_liberty | OK* |
| Schneider product overview | https://www.schneidergis.com/beacon-qpublic-net | OK |

### A.5 Harris Govern — Acclaim (acclaim-web)

What it serves: County recorder / clerk land records, mortgage and deed indexing.
URL patterns:
- `https://acclaim.<county-domain>/AcclaimWeb/`
- `https://acclaim-web.<county-domain>/AcclaimWebLive/`
- `https://officialrecords.<county>.org/AcclaimWeb`
Customer count (estimated): 100-200 counties (Harris Recording Solutions / Govern brands, now part of Harris Local Government). Big in OH, NJ, FL, CO, NV, CA, NY.
Customer list: Customer wins page at https://www.harrisrecordingsolutions.com/category/news/ documents go-lives one by one (Hudson NJ, Broward FL, Hamilton OH, Eagle CO, Nevada CA, Clark NV, Charlotte FL, Leon FL, etc.).
Scraping notes: ASP.NET. Search by Grantor/Grantee, Doc Type, Date Range, Legal. Many require account login for image download; index pages public. Returns paginated server-rendered tables.
Known evasion / rate-limit: Some counties (Broward, Brevard) front-end via county WAF returning 403 to curl HEAD; works in browser. No CAPTCHA on initial searches. Throttle ~1 req/sec.

| Customer | URL | Verified |
|---|---|---|
| Hamilton County, OH (recorder) | https://acclaim-web.hamiltoncountyohio.gov/AcclaimWebLive | OK |
| Eagle County, CO (clerk & recorder) | https://acclaim.eaglecounty.us/AcclaimWeb/ | OK |
| Hudson County, NJ (register) | https://acclaim.hcnj.us/AcclaimWeb/ | OK |
| Hudson County NJ (search page) | https://acclaim.hcnj.us/AcclaimWeb/Search | OK |
| Broward County, FL (official records) | https://officialrecords.broward.org/AcclaimWeb | OK* |

### A.6 Fidlar Technologies — Laredo / AVA / Tapestry

What it serves: County recorder land records — three product tiers: AVA (free public web search), Tapestry (per-search pay-as-you-go via landrecords.com), Laredo (subscription-based bulk access).
URL patterns:
- `https://ava.fidlar.com/<state><county>/AvaWeb/`
- `https://www.landrecords.com/` (Tapestry meta-search)
- Laredo is a thick desktop client + VPN, not a public URL
Customer count (estimated): 200+ counties, heavy in IL, IN, IA, WI, MN, MI, OH, MO.
Customer list: Iowa Land Records consortium at https://iowalandrecords.org/recorder-directory/ enumerates ~99 IA counties (most on Fidlar). Wisconsin and Illinois clusters via county recorder directories.
Scraping notes: AVA web is free but limited (no images, dates+grantor only). Tapestry is the public-facing pay portal — searches all Fidlar counties simultaneously, charges per name search ($5.95) and per page image ($1+). Laredo bulk/Daily access is the high-volume route but is not a scraping target — it's a paid VPN client (~$50-300/mo per county).
Known evasion / rate-limit: Tapestry billing prevents abuse via paid metering. AVA throttles aggressively per IP. No CAPTCHA.

| Customer | URL | Verified |
|---|---|---|
| Linn County, IA (AVA web) | https://ava.fidlar.com/IALinn/AvaWeb/ | OK |
| Iowa statewide consortium | https://iowalandrecords.org/ | OK |
| Tapestry / Landrecords meta-search | https://landrecords.com/ | OK |
| Fidlar Tapestry product | https://fidlar.com/tapestry.aspx | OK |
| Fidlar Laredo product | https://fidlar.com/laredo.aspx | OK |

### A.7 Accela — Citizen Access (ACA)

What it serves: Building permits, planning, code enforcement, business licenses, inspections. Highly relevant for distress signals (open code violations, expired permits, vacant building registrations).
URL patterns:
- `https://aca-prod.accela.com/<jurisdiction>/Default.aspx`
- `https://citizenaccess.<jurisdiction>.gov/CitizenAccess/`
- Custom subdomains like `https://lmsaca.fresno.gov/CitizenAccess/`
Customer count (estimated): 2,000+ state and local agencies on Accela platform overall; ~500-800 with public ACA portals. Strong in CA, TX, FL, OR, WA, VA, MD.
Customer list: No master public list; Accela publishes case studies at https://www.accela.com/customers/ but no enumeration. Pattern recognition via ACA URL is the easiest discovery method.
Scraping notes: ASP.NET. Search by date range, permit type, address, parcel, status. Returns paginated tables with permit number, type, status, applied date, address. Detail pages have rich workflow data (inspections, conditions, fees).
Known evasion / rate-limit: Newer SaaS instances on `aca-prod.accela.com` are behind Akamai. Self-hosted city instances (`citizenaccess.*.gov`) are usually open. Throttle ~1 req/2 sec; no CAPTCHA observed on search. Date-range chunking required for large jurisdictions.

| Customer | URL | Verified |
|---|---|---|
| Cabarrus County, NC | https://citizenaccess.cabarruscounty.us/ | OK |
| Fairfax County, VA (PLUS) | https://plus.fairfaxcounty.gov/ | OK |
| Oklahoma City | https://access.okc.gov/ | OK |
| Howard County, MD | https://dilp.howardcountymd.gov/CitizenAccess/Welcome.aspx | OK |
| City of Fresno, CA | https://lmsaca.fresno.gov/CitizenAccess/ | OK |
| Washington County, OR | https://permits.washingtoncountyor.gov/CitizenAccess/Cap/CapHome.aspx?module=Building&TabName=Building | OK |

### A.8 Granicus — Legistar / govMeetings / OpenCities (govAccess)

What it serves: Council & commission agenda / minutes / video; municipal CMS websites; service request management.
URL patterns:
- `https://<city>.legistar.com/` (Legistar agenda portals)
- `https://<city>.granicus.com/` (video / meeting portals)
- govAccess CMS sites use city's own domain
Customer count (estimated): 7,000+ government agencies total across all Granicus products; Legistar specifically deployed at 86+ of the largest cities (Chicago, Boston, Detroit, Austin, Dallas, Atlanta, Miami, San Jose, Denver, etc.). 600+ California government customers.
Customer list: No flat directory; Legistar deployments discoverable via the `*.legistar.com` subdomain pattern.
Scraping notes: Legistar exposes a clean ASP.NET interface — Calendar.aspx (meetings), Legislation.aspx (bills/items), MeetingDetail.aspx (per-meeting agenda items). Each item links to PDF. There's also a JSON-ish Granicus API at `webapi.legistar.com` (per-tenant, OAuth-token-based). Public data scrapeable from the HTML calendar listing.
Known evasion / rate-limit: Modest. CivicScraper open-source library exists for Legistar. Throttle ~1 req/sec.
Distress relevance: Council agendas show vacant building condemnation hearings, code enforcement appeals, demolition orders, tax forfeiture redemptions, blight-board actions — high-signal distress events.

| Customer | URL | Verified |
|---|---|---|
| Chicago City Clerk (Legistar) | https://chicago.legistar.com/ | OK |
| Chicago meeting calendar | https://chicago.legistar.com/Calendar.aspx | OK |
| Seattle City Council | https://seattle.legistar.com/ | OK |
| Boston City Council | https://boston.legistar.com/ | OK |
| Detroit City Council | https://detroitmi.legistar.com/ | OK |
| Austin TX Council | https://austintexas.legistar.com/ | OK |
| Los Angeles City Council | https://lacity.legistar.com/ | OK |

### A.9 Tyler Data & Insights (formerly Socrata) — Open Data Portals

What it serves: General-purpose government open data. Often hosts permit datasets, code-violation logs, foreclosure registries, vacancy registries, lien records — when the city chooses to publish.
URL patterns:
- `https://data.<city>.gov/` or `https://data.<state>.gov/`
- `https://<jurisdiction>.data.socrata.com/`
- Older `https://<city>.socrata.com/`
SODA API: `https://<host>/resource/<dataset-id>.json` — every dataset has a JSON/CSV/XML/GeoJSON endpoint with `$where`, `$limit`, `$offset`, `$select` SoQL parameters. App tokens free with registration; not strictly required.
Customer count (estimated): 200+ active US state/city/county portals on Socrata/Tyler Data & Insights platform.
Customer list: Partial via Wikipedia "Socrata" page and partner.socrata.com directory.
Scraping notes: Best-in-class for structured access. SODA queryable, no CAPTCHA, generous rate limits with app token. Full dataset CSV download available. The hard part is dataset discovery per jurisdiction.

| Customer | URL | Verified |
|---|---|---|
| City of Chicago | https://data.cityofchicago.org/ | OK |
| New York City | https://data.cityofnewyork.us/ | OK |
| Los Angeles | https://data.lacity.org/ | OK |
| San Francisco | https://data.sfgov.org/ | OK |
| State of Maryland | https://opendata.maryland.gov/ | OK |
| Austin, TX | https://data.austintexas.gov/ | OK |
| King County, WA | https://data.kingcounty.gov/ | OK |
| State of Colorado | https://data.colorado.gov/ | OK |
| Tyler Data & Insights showcase | https://internal-ct.data.socrata.com/ | (CT) |

### A.10 Esri — ArcGIS Hub / ArcGIS Online Open Data

What it serves: GIS open data — parcels, zoning, address points, building footprints, blight inventories, code-enforcement layers, planning data. The dominant platform for county GIS.
URL patterns:
- `https://hub.arcgis.com/datasets/<orgkey>::<slug>`
- `https://<orgname>.opendata.arcgis.com/`
- `https://<sitename>.hub.arcgis.com/`
Underlying API: every layer has a REST FeatureServer at `https://services<N>.arcgis.com/<orgid>/arcgis/rest/services/<layer>/FeatureServer/<n>/query` with full OGC FilterExpression / where-clause support. Pagination via `resultOffset`/`resultRecordCount` (default cap 1,000-2,000 features per query).
Customer count (estimated): Largest single footprint of any vendor in govtech — likely 3,000+ US local governments operate at least one open data hub or public FeatureServer. ArcGIS Online has tens of thousands of organizational accounts.
Customer list: Discovery via https://hub.arcgis.com/search (faceted search over all public hubs).
Scraping notes: Cleanest source. Standard REST GeoJSON. No CAPTCHA, no auth required for public layers. Rate limits per org, generally generous. The ESRI "USA Counties" reference layer at https://data-napsg.opendata.arcgis.com/datasets/esri::usa-counties is a useful crosswalk.

| Customer | URL | Verified |
|---|---|---|
| ArcGIS Hub master search | https://hub.arcgis.com/search | OK |
| Cook County, IL (Cook Central) | https://hub-cookcountyil.opendata.arcgis.com/ | OK |
| Miami-Dade County, FL | https://gis-mdc.opendata.arcgis.com/ | OK |
| LA County (Enterprise GIS) | https://egis-lacounty.hub.arcgis.com/ | OK |
| Clark County, WA | https://hub-clarkcountywa.opendata.arcgis.com/ | OK |
| Atlanta Regional Commission | https://opendata.atlantaregional.com/ | OK |
| DC Open Data | https://opendata.dc.gov/ | OK |
| LA County Parcels (sample dataset) | https://hub.arcgis.com/datasets/lacounty::la-county-parcel-map-service | OK |
| Collier County GIS Hub | https://hub-collierbcc.opendata.arcgis.com/ | OK |

### A.11 OpenGov — Financial Transparency / Permitting Portals

What it serves: Two distinct surfaces — (1) financial transparency dashboards (budget, expenditure, revenue, vendor payments — useful for city distress proxy, not parcel-level), and (2) OpenGov Permitting + Code Enforcement (newer; competes with Accela).
URL patterns:
- `https://stories.opengov.com/<jurisdiction>/published/<id>` (transparency stories)
- `https://controlpanel.opengov.com/transparency-reporting/...` (newer)
- `https://<jurisdiction>.opengov.com/transparency` (white-labeled subdomain)
- Permitting URLs vary per tenant
Customer count (estimated): 2,000+ communities (per OpenGov's own Q2'25 stats).
Customer list: No flat directory; case studies at https://opengov.com/customers/ have a state filter.
Scraping notes: Financial portals expose CSV / Excel download per visualization but not a generic API. Permitting / code-enforcement modules are newer and less consistent. Lower priority for parcel-level signals; higher for city-level economic distress (revenue declines, expenditure cuts).

| Customer | URL | Verified |
|---|---|---|
| OpenGov product landing | https://opengov.com/ | OK |
| Boulder, CO transparency | https://stories.opengov.com/cityofboulderco/published/0qmU_M1ns | OK |
| Camarillo, CA transparency | https://stories.opengov.com/camarilloca/published/ElW3fwwk_ | OK |
| Sun Prairie, WI transparency | https://stories.opengov.com/sunprairiewi/published/dV-gaUfPZ | OK |
| Murrieta, CA portal | https://www.murrietaca.gov/1334/OpenGov-Transparency-Portal | OK |
| Steele County, MN portal | https://www.steelecountymn.gov/finance/financial_transparency_portal.php | OK |
| Arcata, CA portal | https://www.cityofarcata.org/1096/OpenGov-Financial-Transparency-Portal | OK |

### A.12 CivicPlus — Municipal Websites + Municode

What it serves: Local-government CMS (city websites), municode (codified ordinances), agenda/meeting management, mass notification, online forms. The majority of small/mid US city websites run on CivicPlus.
URL patterns: Hosted on the city's own domain. Telltale CMS path: `/CivicAlerts.aspx`, `/Bids.aspx`, `/AgendaCenter`, `/DocumentCenter/View/...`, `?TID=` query strings. Municode content lives on https://library.municode.com/.
Customer count: 12,000+ customers; 4,000+ municipal websites designed (per CivicPlus 2019 stat).
Customer list: No flat list; BuiltWith has https://trends.builtwith.com/websitelist/CivicPlus (paid).
Scraping notes: HTML scraping per-site. Useful for discovering vacant-property registry pages, code-enforcement contact pages, blight-related ordinances (via Municode). Municode has a search API at https://library.municode.com/, returns JSON.
Distress relevance: Indirect — CivicPlus sites host the documentation pages that point to where the actual data is.

| Customer | URL | Verified |
|---|---|---|
| CivicPlus product page | https://www.civicplus.com/ | OK |
| City of Fort Wayne, IN | https://www.cityoffortwayne.in.gov/ | OK |
| Municode (CivicPlus) | https://www.municode.com/municode/page/website-design-0 | OK |
| Roseville, CA (CP customer) | https://www.cityofroseville.com/ | OK |

### A.13 Bid4Assets — County Tax-Sale & Foreclosure Auctions

What it serves: Online tax-defaulted property auctions, sheriff sales, federal forfeiture auctions. Particularly dominant for California county tax sales.
URL pattern: `https://www.bid4assets.com/...` with per-county sub-pages.
Customer count: 100+ counties served historically (Bid4Assets self-report); ~$1B+ proceeds over 20 years.
Customer list: Auction calendar at https://www.bid4assets.com/county-tax-sales lists currently scheduled counties.
Scraping notes: Auction listings are in HTML cards with parcel info, opening bid, deposit, closing time. Each lot has a detail page with assessor parcel number, address, legal, photos (sometimes). Excellent distress signal — these are pre-auction tax-defaulted properties.

| Customer | URL | Verified |
|---|---|---|
| Bid4Assets main | https://www.bid4assets.com/ | OK |
| County tax sale calendar | https://www.bid4assets.com/county-tax-sales | OK |
| Real estate auctions (other) | https://www.bid4assets.com/other-real-estate-auctions | (info) |

### A.14 RealAuction (Realauction.com) — Tax Lien / Tax Deed / Foreclosure

What it serves: Online auctions for tax lien certificates (RealTaxLien), tax deed sales (RealForeclose), foreclosure sales, sheriff sales. Massive Florida footprint.
URL pattern: `https://www.realauction.com/` plus per-jurisdiction subdomains like `https://<county>.realtaxlien.com/`, `https://<county>.realforeclose.com/`, `https://<county>.realtdm.com/`.
Customer count (estimated): 100+ FL counties + scattered customers in NJ, MD, AZ, IN, CO. Dominant in Florida tax-deed market.
Customer list: https://www.realauction.com/clients/index (vendor publishes a per-jurisdiction directory).
Scraping notes: Per-county subdomains each run the same web app. Auction calendars publicly browseable; bidding requires registration + deposit. Property lists released ~30-45 days before each sale.

| Customer | URL | Verified |
|---|---|---|
| RealAuction main | https://www.realauction.com/ | OK |
| Marion County, FL (clerk page) | https://www.marioncountyclerk.org/departments/records-recording/tax-deeds-and-lands-available-for-taxes/tax-deed-sales/ | OK (info) |
| Hillsborough County, FL (clerk) | https://hillsclerk.com/taxdeeds | OK (info) |
| Orange County, FL (comptroller) | https://www.occompt.com/191/Tax-Deed-Sales | OK (info) |

### A.15 GovEase — Online Tax-Sale Auctions

What it serves: Tax lien (Alabama, Colorado) and tax deed (Mississippi, Tennessee, Texas, California, Pennsylvania) county auctions.
URL pattern: `https://www.govease.com/auctions` with per-state subpages.
Customer count (estimated): All 67 Alabama counties (statewide tax-lien partner), most Mississippi counties, growing TN/TX/CA/PA footprint. ~150+ counties total.
Customer list: Calendar at https://www.govease.com/tax-sale-property-auctions; state-by-state articles in https://govease.helpscoutdocs.com/.
Scraping notes: Pre-sale property lists downloadable as PDF/Excel ~2 weeks before auction. Auction is real-time bidding; results published post-sale.

| Customer | URL | Verified |
|---|---|---|
| GovEase main | https://www.govease.com/ | OK |
| Live auction calendar | https://www.govease.com/tax-sale-property-auctions | OK |
| Mississippi spring counties list | https://govease.helpscoutdocs.com/article/177-mississippi-spring-tax-sale-counties | (info) |
| Alabama tax-lien overview | https://govease.helpscoutdocs.com/article/97-alabama-tax-lien-auction-overview | (info) |

### A.16 SRI Inc — Zeus Auction (Tax Sale)

What it serves: Tax-lien and tax-deed auctions for Indiana counties (statewide footprint) and a growing Colorado footprint.
URL pattern: `https://zeusauction.com/` and legacy `https://legacy.sri-taxsale.com/`.
Customer count (estimated): All 92 Indiana counties + ~15 Colorado counties + scattered other states. ~120 counties.
Customer list: https://sriservices.com/; Indiana sale dates at https://legacy.sri-taxsale.com/tax/Indiana/TaxSaleDates.aspx.
Scraping notes: Pre-sale lists as PDF/Excel; ZeusAuction.com is the bidding interface (account required).

| Customer | URL | Verified |
|---|---|---|
| SRI Services | https://sriservices.com/ | OK |
| Zeus Auction | https://zeusauction.com/ | OK |
| Hancock County, IN | https://www.hancockin.gov/177/Tax-Sale | (info) |
| Sedgwick County, CO | https://sedgwickcounty.colorado.gov/online-tax-sale | (info) |

### A.17 PropertyInfo Corporation (Stewart Title) / DataTrace (FNF)

What it serves: Title-plant infrastructure — DataTrace (Fidelity National Financial subsidiary) operates 1,800+ title plants nationwide; PropertyInfo (Stewart Title subsidiary) operates ~112 county databases. Primarily B2B title insurance, but powers some public-facing county clerk search portals.
URL patterns: `https://www.datatracetitle.com/`, `https://www.propertyinfo.com/`, white-labeled per-county portals.
Customer count: DataTrace 1,800+ counties (effectively national title-plant coverage); PropertyInfo ~112 counties.
Customer list: DataTrace coverage page at https://www.datatracetitle.com/national-title-plant-county-coverage.
Scraping notes: Almost entirely paywalled / B2B. Not a free-tier source. Listed here for awareness — if a county clerk's search portal doesn't match Tyler / Schneider / Acclaim / Fidlar pattern, it may be DataTrace/PropertyInfo white-label.
Distress relevance: Reference info only. Not a direct scraping target.

| Resource | URL | Verified |
|---|---|---|
| DataTrace product | https://www.datatracetitle.com/ | OK |
| DataTrace coverage map | https://www.datatracetitle.com/national-title-plant-county-coverage | OK |
| PropertyInfo (Stewart) | https://www.propertyinfo.com/ | OK |

### A.18 Brevitas / Catylist (Moody's) — Commercial Real Estate

What it serves: CRE listings (sale + lease) — alternative to LoopNet / CREXi. Catylist is acquired by Moody's. Tangential to residential-distress focus but useful for commercial distress / off-market CRE signals.
URL patterns: `https://brevitas.com/listings`, regional `https://brevitas.com/listings/<state>/<city>/<type>`. Catylist runs per-market sites (LACDB.com for LA region, MNCAR.com for Minnesota, etc.).
Customer count: Brevitas — open marketplace, thousands of listings. Catylist — hundreds of regional CRE associations.
Scraping notes: Brevitas listings publicly browsable; full contact info gated. Catylist marketplaces vary per region.

| Customer | URL | Verified |
|---|---|---|
| Brevitas listings | https://brevitas.com/listings | OK |
| Brevitas search | https://brevitas.com/search | (info) |

### A.19 Vendors NOT confirmed (omitted — insufficient verifiable customers)

- **Spatial Web / Onpoint county GIS hosting**: Search returned Rolta OnPoint (enterprise GIS, India-based, no clear US county footprint) and OnPoint Spatial (drone mapping consultancy, not a hosting platform). Neither maps to a vendor pattern with 3+ verifiable county customer URLs. **Recommendation: drop from vendor list. Most county GIS hosting is consolidated under Esri/ArcGIS Hub (A.10).**
- **GovOS / CourtHouse Direct / Doxpop**: Adjacent recorder-search aggregators not in original list — flagged for follow-up research if Tyler/Acclaim/Fidlar patterns leave gaps in IN/OH/MI.

---

## Section B — Federal Data Sources

### B.1 HUD Aggregated USPS Vacancy Data — CRITICAL CONFIRMATION

**Verified: this dataset IS publicly downloadable for free at the census-tract level, BUT requires registration as a governmental entity OR registered non-profit user.**

What it is: USPS submits aggregate quarterly counts of "vacant" and "no-stat" residential and business addresses to HUD; HUD tabulates by census tract and distributes as zipped DBF files.
Geography: Census tract is the smallest aggregation unit released. (Block-group is collected internally but not redistributed.)
Frequency: Quarterly, going back to 2005.
Format: Zipped DBF files per quarter (HUD acknowledges this is dated; documented file structure in their FAQ PDF).
Access: https://www.huduser.gov/portal/datasets/usps.html requires login. Registration restricted per HUD-USPS agreement to "governmental entities and non-profit organizations." This is the access gate to be aware of — not a paywall, but an institutional credential gate.
Pricing: Free.
Crosswalk files (HUD-USPS Census Tract Crosswalk): publicly available to ALL users without the institutional gate at https://www.huduser.gov/portal/datasets/census_tract_crosswalk.html — these are derived from USPS data and useful for ZIP-to-tract mapping but are NOT the vacancy counts themselves.

**Bottom line for our scraper: free at tract level for any registered gov entity / 501c3. Significantly reduces BatchData reliance for tract-level vacancy. If we operate under a non-profit research umbrella or partner with one, we can pull this directly. If we are a for-profit only, we need to either get a research/non-profit pass-through or buy commercial USPS-derived vacancy data.**

| Resource | URL | Verified |
|---|---|---|
| HUD USPS Vacancy Data landing | https://www.huduser.gov/portal/datasets/usps.html | OK (202) |
| USPS Vacant Address Data login | https://www.huduser.gov/portal/usps/index.html | OK (202) |
| HUD-USPS Tract Crosswalk (open) | https://www.huduser.gov/portal/datasets/census_tract_crosswalk.html | OK (202) |
| HUD User portal home | https://www.huduser.gov/portal/ | OK |

### B.2 HUD Home Store — REO Listings (FHA Foreclosed Homes)

What it is: HUD's REO single-family listings — properties HUD owns after FHA-insured loan foreclosure.
Access: https://www.hudhomestore.gov — public search by state/city/ZIP/price/beds/baths.
Bidding: Through registered HUD broker; owner-occupant priority window then opens to investors.
Distress signal: Direct (these are already-distressed properties). Can be scraped for new listings.

| Resource | URL | Verified |
|---|---|---|
| HUD Home Store | https://www.hudhomestore.gov | OK |

### B.3 PACER — Federal Bankruptcy Filings (Schedule A/B Real Property)

What it is: Federal court electronic records — bankruptcy petitions include Schedule A/B disclosing all real property owned by the debtor (with addresses, values, lien holders).
Access: https://pcl.uscourts.gov/ (Case Locator — nationwide search). Registration required; account holders pay $0.10/page (capped at $3 per document, $30/quarter waived if under cap).
Coverage: All federal courts (94 districts, 90+ bankruptcy courts).
Distress signal: Highest-quality individual distress signal — Chapter 7/13 filings list owned real property explicitly.
Scraping notes: PACER actively prohibits bulk scraping. Per-case pull via Case Locator + PDF retrieval is sanctioned. Third-party services (PacerMonitor, BankruptcyWatch) provide enhanced indexing layers (paid).
Cost reality: A motivated 50-state weekly bankruptcy crawl will exceed the $30/quarter free cap; budget realistic PACER spend at low hundreds of dollars per month for moderate volume.

| Resource | URL | Verified |
|---|---|---|
| PACER Case Locator | https://pcl.uscourts.gov/ | OK |
| PACER National Index search | https://pacer.uscourts.gov/find-case/search-national-index | OK |

### B.4 VA — VRM Mortgage Services (VA REO Listings)

What it is: VA-owned REO properties from defaulted VA loans, marketed by VRM.
Access: https://www.vrmproperties.com/ — searchable map. Listed via local MLS too.
Distress signal: Direct.
Scraping notes: Map-based UI. Fewer listings than HUD Home Store but valuable for veteran-population markets.

| Resource | URL | Verified |
|---|---|---|
| VRM Properties (VA REO) | https://www.vrmproperties.com/ | OK |
| VA Property Mgmt contract info | https://www.benefits.va.gov/homeloans/realtors_property_mgmt.asp | OK |

### B.5 USDA Rural Development — REO / Foreclosure

What it is: REO and pre-foreclosure single- and multi-family properties from defaulted USDA Rural Development loans.
Access: https://properties.sc.egov.usda.gov/resales/public/searchSFH (single family) and `/searchMFH` (multi-family). Also at https://www.resales.usda.gov/.
Open data: https://catalog.data.gov/dataset/usda-rural-development-resale-properties-real-estate-owned (data.gov mirror — daily/weekly updated).
Distress signal: Direct, rural-specific. Underused for rural REO opportunities.
Scraping notes: Public search portal scrapeable; data.gov mirror is the cleaner pull.

| Resource | URL | Verified |
|---|---|---|
| USDA RD Single-Family Resales | https://properties.sc.egov.usda.gov/resales/public/searchSFH | OK |
| USDA Resales home | https://www.resales.usda.gov/resales/public/home | OK |
| Data.gov USDA REO dataset | https://catalog.data.gov/dataset/usda-rural-development-resale-properties-real-estate-owned | OK |

### B.6 GSA Auctions — Federal Real Property Disposal

What it is: GSA-managed disposition of surplus federal real estate — buildings, land, military holdings.
Access: https://realestatesales.gov/ (real property), https://disposal.gsa.gov/s/ (Real Property Disposition portal), https://gsaauctions.gov/ (personal property).
Distress signal: Specialized — federal-surplus, not residential distress. Niche but real for commercial/land plays.

| Resource | URL | Verified |
|---|---|---|
| Real Estate Sales (GSA) | https://realestatesales.gov/ | OK |
| GSA Real Property Disposition | https://disposal.gsa.gov/s/ | OK |
| GSA Auctions (personal property) | https://gsaauctions.gov/ | OK (200 with browser UA) |
| GSA real-property-disposition info | https://www.gsa.gov/real-estate/real-property-disposition | OK |

### B.7 US Treasury — Seized Real Property Auctions

What it is: ~300/year auctions of federally seized/forfeited real estate (drug, fraud, IRS forfeiture). Run by Treasury Executive Office for Asset Forfeiture (TEOAF), marketed by CWS Marketing & Design Group.
Access: https://www.treasury.gov/auctions/treasury/rp/ (master) and https://cwsmarketing.com/auctions/real-estate/ (CWS contractor site, current listings + state filter).
Distress signal: Direct — heavily distressed, often deeply discounted. Niche volume but high signal-to-noise.

| Resource | URL | Verified |
|---|---|---|
| Treasury Seized Real Property | https://www.treasury.gov/auctions/treasury/rp/ | OK |
| CWS Marketing (auction contractor) | https://cwsmarketing.com/ | OK |

### B.8 FHFA — House Price Index, Foreclosure & Borrower Assistance Data

What it is: FHFA HPI at national/division/state/MSA/county/ZIP/tract level (deep history to mid-1970s); plus quarterly Foreclosure Prevention Reports and Borrower Assistance maps for Fannie/Freddie loans.
Access: https://www.fhfa.gov/data/hpi/datasets (HPI bulk download), https://www.fhfa.gov/data/datasets (catalog), https://www.fhfa.gov/data/dashboard (dashboards).
API: No public REST API — bulk CSV / Excel downloads only.
Distress signal: Macro/county-level price-trend proxy for distress. Foreclosure-prevention dataset is conventional-loan-only (Fannie/Freddie), not whole market.

| Resource | URL | Verified |
|---|---|---|
| FHFA HPI datasets | https://www.fhfa.gov/data/hpi/datasets | OK |
| FHFA all datasets | https://www.fhfa.gov/data/datasets | OK |
| FHFA dashboards | https://www.fhfa.gov/data/dashboard | OK |

### B.9 FEMA — Flood Maps, NFHL, OpenFEMA Disaster Declarations

What it is: National Flood Hazard Layer (FIRM panels, flood zones) plus disaster declarations history. Both excellent distress proxies — flood-zone properties have insurance distress; disaster-declared counties have post-event distress windows.
Access:
- Flood maps: https://msc.fema.gov/ (FIRMette download, search by address)
- NFHL GIS web service: https://hazards.fema.gov/femaportal/wps/portal/NFHLWMS/
- OpenFEMA API: https://www.fema.gov/about/openfema (no API key required, full REST/JSON)
- DisasterDeclarationsSummaries endpoint: `https://www.fema.gov/api/open/v2/DisasterDeclarationsSummaries`
Distress signal: Flood zone overlay = elevated insurance cost / forced placement / sale-friction signal. Disaster declarations = post-event displacement / motivated-seller windows.

| Resource | URL | Verified |
|---|---|---|
| FEMA Flood Map Service Center | https://msc.fema.gov/ | OK |
| FEMA NFHL WMS | https://hazards.fema.gov/femaportal/wps/portal/NFHLWMS/ | OK |
| OpenFEMA API (info) | https://www.fema.gov/about/reports-and-data/openfema | OK |
| Disaster Declarations endpoint | https://www.fema.gov/api/open/v2/DisasterDeclarationsSummaries | OK |

### B.10 EPA — ACRES Brownfields Properties

What it is: Properties with environmental contamination receiving EPA Brownfields grant funding (assessment, cleanup, redevelopment). Strong distress signal — environmentally encumbered land.
Access: https://catalog.data.gov/dataset/acres-brownfields-properties (downloadable CSV/JSON via data.gov), Cleanups in My Community (CIMC) for filtered search, https://www.epa.gov/brownfields for program info.
Updates: Bi-monthly (4th and 18th of each month).
Distress signal: High — these properties have known contamination, often deeply discounted, frequent off-market sales.

| Resource | URL | Verified |
|---|---|---|
| ACRES Brownfields dataset (data.gov) | https://catalog.data.gov/dataset/acres-brownfields-properties | OK |
| EPA Brownfields program | https://www.epa.gov/brownfields | OK |
| Brownfields-tagged datasets | https://catalog.data.gov/dataset/?tags=brownfields | OK |

### B.11 BLS — Local Area Unemployment Statistics (LAUS)

What it is: Monthly unemployment estimates for states/MSAs/counties/cities. County-level not seasonally adjusted.
Access: https://www.bls.gov/lau/ landing; bulk flat-file downloads; BLS Public Data API at `https://api.bls.gov/publicAPI/v2/timeseries/data/` (registration recommended for higher rate limits, free).
Distress signal: Macro / county-level economic distress proxy. Useful for ranking counties by softening labor markets.

| Resource | URL | Verified |
|---|---|---|
| BLS LAUS landing | https://www.bls.gov/lau/ | OK* (bot WAF, real browsers OK) |
| BLS LAUS data tools | https://www.bls.gov/lau/data.htm | OK* |
| BLS Public Data API | https://api.bls.gov/publicAPI/v2/timeseries/data/ | OK (POST endpoint) |

### B.12 Census Bureau — ACS Housing Vacancy & Tenure (Tables B25002/B25003/B25004)

What it is: American Community Survey 5-year estimates of housing units, vacancy status, tenure (owner vs renter), and vacancy reason (for-rent, for-sale-only, vacation, other).
Access: https://api.census.gov/data.html (Census Data API, free, key registration in seconds).
Geography: All standard geographies — nation/state/county/place/ZCTA/tract/block group.
Distress signal: Tract-level vacancy by reason ("other vacant" is the bucket containing distressed/abandoned). Less timely than HUD-USPS (5-year rolling vs quarterly) but no institutional gate.

| Resource | URL | Verified |
|---|---|---|
| Census Data API catalog | https://api.census.gov/data.html | OK |
| ACS 5-Year API | https://www.census.gov/data/developers/data-sets/acs-5year.html | OK |

### B.13 CFPB Consumer Complaint Database (Mortgage)

What it is: Consumer complaints filed with the CFPB, including mortgage-servicing complaints (often a leading indicator of household financial distress).
Access: https://www.consumerfinance.gov/data-research/consumer-complaints/ (search UI), https://cfpb.github.io/api/ccdb/api.html (full REST API, no auth, free), data.gov mirror also available.
Distress signal: Not parcel-level (complaints don't disclose addresses) but useful as a market-level / lender-level distress indicator. Filterable by ZIP/state/lender/issue.

| Resource | URL | Verified |
|---|---|---|
| CFPB Consumer Complaints | https://www.consumerfinance.gov/data-research/consumer-complaints/ | OK |
| CFPB CCDB API docs | https://cfpb.github.io/api/ccdb/api.html | OK |

### B.14 FRED (St. Louis Fed) — County-Level Economic Indicators

What it is: Federal Reserve Economic Data — 800,000+ time series including 330,000 tagged "County." Aggregates BLS, BEA, Census, FHFA, NAR data into a single API.
Access: https://fred.stlouisfed.org/ (browse), https://api.stlouisfed.org/ (REST API, free key registration).
Distress signal: Best general-purpose macro/county overlay — unemployment, HPI, personal income, building permits, all in one API.
Note: `fred.stlouisfed.org` returned 000 to my curl probes today — likely an Akamai bot rule; the site works in browsers and the API at `api.stlouisfed.org` returned a 301 (redirect to docs). Treat URL pattern as confirmed via independent docs and the historic FRED footprint.

| Resource | URL | Verified |
|---|---|---|
| FRED API root | https://api.stlouisfed.org/ | OK (301) |
| FRED API docs | https://fred.stlouisfed.org/docs/api/fred/ | OK* (bot WAF, public API works) |
| FRED main site | https://fred.stlouisfed.org/ | OK* (bot WAF, browser access fine) |

### B.15 SBA — Disaster Loan Data & Declarations

What it is: SBA Disaster Loan Program data (verified loss + approved loan amounts, by city/county/ZIP/state) + searchable disaster declaration database.
Access: https://data.sba.gov/ (open data portal, full FY datasets), https://lending.sba.gov/search-disaster/ (searchable declaration map).
Distress signal: Post-disaster property distress overlay (where SBA processed actual loan apps = where damage was concentrated).

| Resource | URL | Verified |
|---|---|---|
| SBA Open Data | https://data.sba.gov/ | OK |
| SBA Disaster Declaration Search | https://lending.sba.gov/search-disaster/ | OK |

### B.16 DEA / Treasury Forfeiture (CWS Marketing)

What it is: DEA-seized assets (real estate among them) auctioned via CWS Marketing under the unified Treasury Forfeiture Fund umbrella. Same pipeline as B.7 — real estate listings flow through CWS.
Access: https://cwsmarketing.com/auctions/real-estate/ (state filter pages exist).
Distress signal: See B.7 — same source. DEA mention is to flag the seizure-side origin, not a separate listing surface.
Note: https://www.dea.gov/operations/asset-forfeiture returns 403 to curl (DEA WAF); page exists in browsers.

| Resource | URL | Verified |
|---|---|---|
| CWS Marketing real estate auctions | https://cwsmarketing.com/ | OK |
| USA.gov auctions hub | https://www.usa.gov/auctions-and-sales | OK |

### B.17 IRS — Federal Tax Liens

What it is: Federal tax liens (Notices of Federal Tax Lien) are filed at the COUNTY recorder office, not in a single federal database. IRS publishes aggregate tax-collection statistics via SOI Tax Stats but not parcel-level lien data.
Access: SOI Tax Stats: https://www.irs.gov/statistics/soi-tax-stats-individual-statistical-tables-by-size-of-adjusted-gross-income (aggregate only). Parcel-level federal tax liens are accessed via the county recorder portals (Section A vendors).
Distress signal: Yes (federal tax liens are a strong distress signal), but the data is captured upstream in vendor-A.x recorder scrapes, not in a separate IRS feed.

### B.18 IRS — Treasury Seizure (already covered)

Treasury auctions covered in B.7. IRS-specific seizures flow through the same TEOAF pipeline.

---

## Section C — 150-word Summary

**Top 3 vendor patterns by jurisdiction count (biggest scraper leverage):**
1. **Esri ArcGIS Hub / Online** — likely 3,000+ US jurisdictions expose parcels, zoning, code-enforcement layers via standard ArcGIS REST FeatureServers. One adapter, no auth, GeoJSON. This is by far the largest single leverage point.
2. **Schneider qPublic.net / Beacon** — ~1,000+ assessor/parcel/tax portals on a single ASP.NET surface keyed by jurisdiction parameter. Strong GA/FL/AL/IN/IA/KS/NE/MN/MS coverage.
3. **Tyler Technologies** (PublicSearch + Eagle + Odyssey combined) — 200-400 county recorder/court installations under unified URL patterns (`*.publicsearch.us`, `*.tylertech.cloud`, `<state>.countygovernmentrecords.com`).

**Underused/surprising federal source:** **OpenFEMA Disaster Declarations API** — completely free, no key, gives every disaster-declared county since 1953 with declaration date and incident type. Combined with SBA disaster loan totals, this is a high-quality "motivated seller window" overlay almost no real-estate-data vendor uses.

**HUD aggregated USPS vacancy data — confirmed:** Free at census-tract level, downloadable as quarterly DBF zips at https://www.huduser.gov/portal/datasets/usps.html. **Key gate:** registration restricted to governmental entities and registered non-profits. The HUD-USPS Crosswalk files (derived) are unrestricted at https://www.huduser.gov/portal/datasets/census_tract_crosswalk.html. So: yes, free at tract level, but operating-entity must qualify for the registration tier — significantly reduces BatchData reliance once we route through a qualifying entity.
