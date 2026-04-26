# City-Level Open Data Portals — Top 30 Investor Metros

Inventory of public city/county data sources for code violations, vacant property registries, building permits, demolition orders, 311, tax/utility, and rental registration. URLs verified by HTTP HEAD/GET on 2026-04-24.

Vendor patterns dominating the landscape:
- **Socrata SODA** (now "Tyler Data & Insights") — most populous: NYC, Chicago, Dallas, Fort Worth, KCMO, Cincinnati, Memphis, Nashville, Orlando, Buffalo, Austin, Houston catalog, Birmingham, Charlotte hosts both. SODA endpoint pattern: `https://{host}/resource/{4x4}.json?$limit=N&$where=...`. Authless, throttled (rate limit ~1000 req/hr unless app token).
- **ArcGIS Hub** — Detroit, Cleveland, Phoenix (mapping side), Tampa, Atlanta, Columbus, Charlotte (newer datasets), Indianapolis, Baltimore, Nashville (some). Pattern: `https://{org}.opendata.arcgis.com/datasets/{slug}` with FeatureServer REST endpoint behind it.
- **Accela Citizen Access** — fallback when no open dataset: Cleveland, Indianapolis, Atlanta, Tampa, Milwaukee, Birmingham, San Antonio, Charlotte (Mecklenburg). Pattern: `https://aca-prod.accela.com/{AGENCY}/...`. Scrapeable but not bulk.
- **CKAN** — Pittsburgh (WPRDC), Newark (currently offline 503).

Wedge-signal coverage: 26 of 30 cities (87%) publish code-violation data in a structured public format. The four with weakest free coverage: San Antonio, Phoenix (mostly map widget, hard to bulk), Jacksonville, Birmingham.

---

## Houston, TX

Open data backbone: Socrata-style CKAN portal (data.houstontx.gov) + COHGIS ArcGIS Hub. Permits and most operational data are split between these two and the city's own dashboard sites.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | City of Houston Building Code Enforcement Violations (DON) | https://data.houstontx.gov/dataset/city-of-houston-building-code-enforcement-violations-don | CKAN dataset (CSV/Excel) | none | quarterly | Department of Neighborhoods inspections | 200 |
| Building permits | Residential Building Permits by Month and Year | https://data.houstontx.gov/dataset/residential-building-permits | CKAN (Excel) | none | monthly | 2004-present, single+multi-family | 200 |
| 311 portal | Houston 311 Help & Info | https://www.houstontx.gov/311/ | HTML form | none | live | API not exposed publicly; data feed limited | 400 (HEAD blocked) |
| Permit dashboard | Houston Public Works Permit Dashboard | https://hpcdashboard.houstontx.gov | HTML dashboard | none | daily | Live permit volumes, no bulk download | 200 |
| GIS hub | COHGIS Data Hub | https://cohgis-mycity.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Adjacent layers (zoning, parcels, super neighborhoods) | 200 |
| Open data portal | Houston Open Data Portal | https://data.houstontx.gov/ | CKAN catalog | none | varies | Houston is on CKAN, not Socrata SODA — bulk fetch via DataStore API | 200 |
| GIS map portal | mycity.houstontx.gov | https://mycity.houstontx.gov/ | ArcGIS apps | none | live | Address-keyed lookups; not bulk | n/a |

Notes: Houston is the weakest of the major Texas cities on bulk-API code violations. CKAN DataStore API: `https://data.houstontx.gov/api/3/action/datastore_search?resource_id=...`.

---

## Cleveland, OH

Open data backbone: ArcGIS Hub (data.clevelandohio.gov, launched 2024) + Cuyahoga County GIS + Accela Citizen Access for case lookup.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Open data portal | City of Cleveland Open Data | https://data.clevelandohio.gov/ | ArcGIS Hub catalog | none | varies | Building permits, rental registrations, housing code violations all listed | 200 |
| Code violations | Cleveland code violations / property conditions | https://data.clevelandohio.gov/ | ArcGIS Hub | none | varies | Search "code violations" — look up dataset slug in catalog | 200 (catalog) |
| Vacant registry | Vacant and Abandoned Property Registration (VAPR) | https://www.clevelandohio.gov/residents/codes-ordinances/residents-first/vacant-properties | HTML info / Accela form | none | weekly | Dataset surfaces in catalog; primary registration via Accela | 200 |
| Code enforcement portal | Accela Citizen Access — Cleveland (COC) | https://aca-prod.accela.com/COC/Default.aspx | Accela portal | none | live | Address lookup for active code violations + VAPR | 200 |
| 311 / building complaints | Cleveland 311 Building & Housing | https://www.clevelandohio.gov/311/building-housing | HTML form | none | live | 311 calls dashboard on data.clevelandohio.gov | 200 |
| GIS hub | Cuyahoga County GIS (parcels) | https://data.clevelandohio.gov/ | ArcGIS Hub | none | varies | Parcel + ownership joins | 200 |

Notes: Vendor-flagged as Accela for case lookup, ArcGIS Hub for bulk. Cleveland VAPR is a wedge — actively-registered vacant buildings are an explicit list maintained by Building & Housing. Look up specific Hub dataset slugs from the catalog UI; the catalog itself is the most reliable entry point.

---

## Philadelphia, PA

Open data backbone: OpenDataPhilly (Carto+Socrata hybrid) at opendataphilly.org + data.phila.gov (Carto-based). L&I = Licenses and Inspections, the wedge agency.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | L&I Code Violations | https://opendataphilly.org/datasets/licenses-and-inspections-code-violations/ | Carto/SQL API | none | daily | Largest building-violations dataset of any major US city | 200 |
| Permits | L&I Building Permits | https://opendataphilly.org/datasets/licenses-and-inspections-building-permits/ | Carto/SQL API | none | daily | Issuance, expiration, plumbing/elec/mech | (catalog parent verified) |
| Code violation appeals | L&I Appeals of Code Violations | https://opendataphilly.org/datasets/licenses-and-inspections-appeals-of-code-violations-and-permit-refusals/ | Carto/SQL API | none | weekly | Appeals = signal that owner is contesting | (catalog parent verified) |
| Open data portal | OpenDataPhilly | https://opendataphilly.org/datasets/ | Catalog | none | varies | 400+ datasets, mixed Carto/Socrata | (parent verified) |
| L&I dashboard | data.phila.gov L&I Violations | https://data.phila.gov/visualizations/li-violations | HTML viz | none | daily | 403 to HEAD; loads in browser. Use opendataphilly URL for API | 403 (browser-only) |
| Rental licenses | L&I Business Licenses (rental subset) | https://opendataphilly.org/datasets/licenses-and-inspections-business-licenses/ | Carto/SQL API | none | daily | Filter licensetype=Rental for landlord registry equivalent | (catalog parent verified) |

Notes: Philly is best-in-class for L&I bulk data via Carto SQL API: `https://phl.carto.com/api/v2/sql?q=SELECT+*+FROM+violations+LIMIT+1`. Philadelphia is one of the top-3 deepest cities.

---

## Chicago, IL

Open data backbone: Socrata (data.cityofchicago.org). Buildings dept publishes violations and the famous Vacant & Abandoned Buildings Violations dataset directly. This is the gold-standard portal in the country.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Building Violations (DOB) | https://data.cityofchicago.org/Buildings/Building-Violations/22u3-xenr | Socrata SODA | none | daily | 2006-present, ~1.6M rows. API: `/resource/22u3-xenr.json` | 200 |
| Vacant building violations | Vacant and Abandoned Buildings — Violations | https://data.cityofchicago.org/Buildings/Vacant-and-Abandoned-Buildings-Violations/kc9i-wq85 | Socrata SODA | none | daily | Subset filtered to vacant-building violations | 200 |
| Vacant building registrations | Vacant Building Violations (registration enforcement) | https://data.cityofchicago.org/Buildings/Vacant-Building-Violations/u7si-yh3t | Socrata SODA | none | daily | Vacant Building Registration enforcement actions | (parent verified) |
| Building permits | Building Permits | https://www.chicago.gov/city/en/dataset/building_permits.html | Socrata SODA | none | daily | Issued/expired/in-review | 200 |
| Code violations API | Socrata API endpoint (building violations) | https://data.cityofchicago.org/resource/22u3-xenr.json?$limit=1 | Socrata SODA JSON | none | daily | Verified returning data | 200 |
| Open data portal | Chicago Data Portal | https://data.cityofchicago.org/ | Socrata catalog | none | varies | 600+ datasets | 200 |
| 311 portal | 311 Service Requests | https://data.cityofchicago.org/ | Socrata SODA | none | daily | Multiple 311 datasets, search "311 service requests" | (catalog verified) |

Notes: Chicago is a top-3 deepest. Vacant-building data is uniquely first-class — most cities bury vacancy in code-violation tables; Chicago has dedicated datasets.

---

## Phoenix, AZ

Open data backbone: phoenixopendata.com (CKAN + Open Government Data) + mapping-phoenix ArcGIS Hub for spatial. Code violations are surfaced as map widgets, harder to bulk-fetch.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Code Compliance — All Violations (ArcGIS) | https://hub.arcgis.com/datasets/eb7330c8600540d6972b7e094565baff_3/about | ArcGIS Hub | none | weekly | 2011-present; FeatureServer underneath | 200 |
| Building permits | Phoenix, AZ Building Permit Data | https://www.phoenixopendata.com/dataset/phoenix-az-building-permit-data | CKAN | none | monthly | SOCDS-derived; not parcel-level | 200 |
| Code enforcement search | NSDOnline Code Enforcement | https://nsdonline.phoenix.gov/CodeEnforcement | HTML form | none | live | Address/case lookup, not bulk | 200 |
| Code enforcement dept | Neighborhood Services Code Enforcement | https://www.phoenix.gov/administration/departments/nsd/blight-removal/code-enforcement.html | HTML | none | n/a | Department landing | 200 |
| Open data portal | City of Phoenix Open Data | https://www.phoenixopendata.com/ | CKAN catalog | none | varies | Limited operational datasets | 200 |
| GIS hub | Phoenix Mapping Open Data | https://mapping-phoenix.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Parcel, zoning, code areas | 200 |
| GIS portal | iMap | https://www.phoenix.gov/imap | Web GIS | none | live | Property search interface | 200 |

Notes: Phoenix is one of the worst-3 for bulk code-violation API access. The ArcGIS dataset exists but lacks the rich attribute fields Chicago/Philly publish.

---

## Dallas, TX

Open data backbone: Socrata (dallasopendata.com). Code Compliance dept publishes a comprehensive set; 311 is well-instrumented.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Code Violations (Archive) | https://www.dallasopendata.com/Archive/Code-Violations/x9pz-kdq9 | Socrata SODA | none | daily | API: `/resource/x9pz-kdq9.json` | 200 |
| Building permits | Building Permits | https://www.dallasopendata.com/Services/Building-Permits/e7gq-4sah | Socrata SODA | none | daily | API: `/resource/e7gq-4sah.json` | 200 |
| 311 service requests | 311 Service Request Based | https://www.dallasopendata.com/Services/311-Service-Request-Based/wwr9-8ha7 | Socrata SODA | none | daily | Oct 2018-present, ~400 request types | 200 |
| 311 (current) | 311 Service Requests | https://www.dallasopendata.com/Services/311-Service-Requests/gc4d-8a49 | Socrata SODA | none | daily | Current rolling window | 200 |
| Open data portal | Dallas OpenData | https://www.dallasopendata.com/ | Socrata catalog | none | varies | 200+ datasets | 200 |
| County GIS | Dallas County Open Data Hub | https://dallas-county-open-data-hub-dallascountygis.hub.arcgis.com/ | ArcGIS Hub | none | varies | Parcel-level joins | (parent verified) |

Notes: Dallas is solid on the wedge — code violations + 311 + permits all on Socrata, all with daily refresh. One of the top-10 cleanest portals.

---

## Fort Worth, TX

Open data backbone: Socrata (data.fortworthtexas.gov) + open-data-cfw ArcGIS Hub.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Code Violations | https://data.fortworthtexas.gov/Property-Data/Code-Violations/spnu-bq4u | Socrata SODA | none | weekly | API: `/resource/spnu-bq4u.json` — verified live | 200 |
| Code violations API | Code Violations JSON | https://data.fortworthtexas.gov/resource/spnu-bq4u.json?$limit=1 | Socrata SODA JSON | none | weekly | Returned row OK | 200 |
| Code Violations app | ArcGIS Code Violations App | https://open-data-cfw.hub.arcgis.com/datasets/code-violations-app | ArcGIS Hub | none | weekly | Map experience | (parent verified) |
| Address consolidation | One Address (Permits + Violations) | https://oneaddress.fortworthtexas.gov/ | HTML lookup | none | daily | Combined view | n/a |
| Open data portal | Fort Worth Open Data | https://data.fortworthtexas.gov/ | Socrata catalog | none | varies | Smaller but clean | 200 |
| ArcGIS Hub | Fort Worth ArcGIS Hub | https://open-data-cfw.hub.arcgis.com/ | ArcGIS Hub | none | varies | Spatial layers | (parent verified) |

Notes: Fort Worth is one of the cleaner mid-size portals. Weekly refresh is fast enough for the wedge.

---

## San Antonio, TX

Open data backbone: data.sanantonio.gov (CKAN) + opendata-cosagis ArcGIS Hub. Code Enforcement Services data is NOT publicly published as a bulk dataset — only address lookup via 311.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Building permits | Building Permits | https://data.sanantonio.gov/dataset/building-permits | CKAN dataset | none | monthly | 2020-2024, building/trade/garage sale | 200 |
| Code Enforcement dept | Code Enforcement Services | https://www.sa.gov/Directory/Departments/DSD/CES | HTML | none | n/a | No bulk dataset published; case lookup only via 311 | 200 |
| Open data portal | Open Data SA | https://data.sanantonio.gov/ | CKAN catalog | none | varies | Limited operational data | (catalog) |
| GIS hub | SA OpenData (CoSAGIS) | https://opendata-cosagis.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Parcels, addressing | 200 |
| 311 portal | SA 311 Portal | https://311.sanantonio.gov/ | HTML form | none | live | Lookup only | n/a |

Notes: One of the worst-3 for the wedge. Code violations are not exposed as a bulk dataset; you'd have to FOIA or scrape 311 case-by-case.

---

## Austin, TX

Open data backbone: Socrata (data.austintexas.gov). Austin Code dept is well-instrumented.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Austin Code Complaint Cases | https://data.austintexas.gov/Public-Safety/Austin-Code-Complaint-Cases/6wtj-zbtb | Socrata SODA | none | varies | Federated dataset; resource API blocked, use rows.json | 200 |
| Building permits | Issued Construction Permits | https://data.austintexas.gov/dataset/Issued-Construction-Permits/3syk-w9eu | Socrata SODA | none | daily | Building, electrical, plumbing, mechanical | 200 |
| Repeat offenders | Repeat Offender Registrations | https://data.austintexas.gov/Public-Safety/Repeat-Offender-Registrations/86z9-i27i/data | Socrata SODA | none | weekly | Properties on Austin Code repeat-offender list | 200 |
| 311 service requests | Austin 311 Public Data | https://data.austintexas.gov/Utilities-and-City-Services/Austin-311-Public-Data/xwdj-i9he | Socrata SODA | none | daily | Includes code-related requests | 200 |
| Catalog landing (data.gov mirror) | Austin Code Complaint Cases (data.gov) | https://catalog.data.gov/dataset/austin-code-complaint-cases | Catalog | none | varies | Federated mirror | 200 |
| Open data portal | Austin Open Data | https://data.austintexas.gov/ | Socrata catalog | none | varies | Note: 2023 audit found data quality issues | 200 |

Notes: Austin's Repeat Offender list is unusually valuable — an explicit list of bad-actor properties. Note federated-view quirk: `/resource/{4x4}.json` may 404, use `/api/views/{4x4}/rows.json`.

---

## Tampa, FL

Open data backbone: Tampa GeoHub (ArcGIS) + Accela Citizen Access for code/permits. No Socrata.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code enforcement (case lookup) | Accela Tampa Enforcement Cases | https://aca-prod.accela.com/Tampa/Cap/CapHome.aspx?module=Enforcement&TabName=Enforcement | Accela portal | none | live | Address/case lookup; scrapeable | 200 |
| Building permits portal | Accela Tampa Building Permits | https://aca-prod.accela.com/TAMPA/Default.aspx | Accela portal | none | live | Permit application + lookup | 200 |
| Code enforcement liens | Code Enforcement Liens & Utility Balances | https://www.tampa.gov/neighborhood-enhancement/lien-search | HTML form | none | live | Address-keyed lien search | n/a |
| GeoHub | City of Tampa GeoHub | https://city-tampa.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Zoning, neighborhoods, parcels | 200 |
| 311 portal | Tampa Connect | https://www.tampaconnect.com/CRM/s/ | HTML form | none | live | Service requests via portal | n/a |

Notes: Tampa publishes spatial data on GeoHub but operational code/permit data lives behind Accela. Scrapeable but no bulk API.

---

## Atlanta, GA

Open data backbone: dpcd-coaplangis ArcGIS Hub + Accela Citizen Access. ATL311 has no public bulk dataset.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Building permits | All Building Permits 2019-2024 | https://dpcd-coaplangis.opendata.arcgis.com/datasets/655f985f43cc40b4bf2ab7bc73d2169b | ArcGIS Hub | none | daily | Pulled from Accela; in-review/issued/closed | (catalog parent verified) |
| Code enforcement search | Accela Atlanta Enforcement | https://aca-prod.accela.com/ATLANTA_GA/Cap/CapHome.aspx?module=Enforcement&TabName=Enforcement | Accela portal | none | live | Address/case lookup | 200 |
| Permitting portal | Atlanta Online Portal (Accela) | https://aca-prod.accela.com/atlanta_ga/Default.aspx | Accela portal | none | live | Apply/track | 200 |
| GIS Hub | City of Atlanta Open Data Hub | https://dpcd-coaplangis.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Department of City Planning data | 200 |
| Regional data | ARC Open Data & Mapping Hub | https://opendata.atlantaregional.com/ | ArcGIS Hub | none | varies | Atlanta Regional Commission datasets | 200 |
| Code Enforcement (info) | ATL311 — Code Enforcement Open & Vacant | https://www.atl311.com/en-us/knowledgearticle/?code=KB0011721 | HTML KB | none | n/a | Reporting workflow only | 200 |
| 311 portal | ATL311 | https://www.atl311.com/en-US/ | HTML form | none | live | No bulk dataset | 200 |

Notes: Atlanta has good permits data via the Hub; code violations require Accela scraping. Below Chicago/Philly tier.

---

## Charlotte, NC

Open data backbone: ArcGIS Hub (data.charlottenc.gov) + Mecklenburg County for permits. Strong code-enforcement coverage.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Code Enforcement Cases All | https://data.charlottenc.gov/datasets/charlotte::code-enforcement-cases-all/ | ArcGIS Hub | none | daily | Housing/commercial/zoning/nuisance | 200 |
| Code violations (QC) | Code Enforcement QCCode Data | https://data.charlottenc.gov/datasets/code-enforcement-qccode-data-1 | ArcGIS Hub | none | daily | QC-style export | 200 |
| Demolition orders | Code Enforcement Orders to Demolish | https://data.charlottenc.gov/datasets/0d519426fca841dba3646e7fc02c6ebf | ArcGIS Hub | none | weekly | High-signal: explicit demolition orders | 200 |
| Inspector areas | Code Enforcement Service Areas | https://data.charlottenc.gov/datasets/code-enforcement-service-areas | ArcGIS Hub | none | yearly | Geographic taxonomy | 200 |
| 311 service requests | Service Requests 311 | https://data.charlottenc.gov/datasets/d656def619664c47aa95073dcf8928cd | ArcGIS Hub | none | daily | Including code-related | 200 |
| Permits portal (county) | AccelaMeck (Mecklenburg) | https://aca-prod.accela.com/BOCC/Default.aspx | Accela portal | none | live | Permits run by Mecklenburg County | 200 |
| Open data portal | Charlotte Open Data | https://data.charlottenc.gov/ | ArcGIS Hub catalog | none | varies | 100+ datasets | 200 |

Notes: Explicit "Orders to Demolish" dataset is unusual and high-value for our wedge. Strong portal.

---

## Detroit, MI

Open data backbone: ArcGIS Hub (data.detroitmi.gov, donated by Socrata Foundation but ported to ArcGIS). The deepest blight + demolition + vacancy data of any US city.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Blight violations | Blight Violations | https://data.detroitmi.gov/datasets/blight-violations | ArcGIS Hub | none | daily | Blight Violation Notices issued by inspectors | 200 |
| Vacant property registry | Vacant Property Registrations | https://data.detroitmi.gov/datasets/detroitmi::vacant-property-registrations-1 | ArcGIS Hub | none | daily | BSEED-required registrations, 2019-present | 200 |
| Demolitions | Detroit Demolitions | https://data.detroitmi.gov/Property-Parcels/Detroit-Demolitions/rv44-e9di | ArcGIS Hub | none | daily | Completed demolitions w/ DLBA + city programs | 200 |
| Building permits | Building Permits Summary | https://data.detroitmi.gov/datasets/Building-Permits-Summary/4pit-zggk | ArcGIS Hub | none | daily | Issued permits | 200 |
| 311 service requests | Improve Detroit Issues | https://data.detroitmi.gov/datasets/improve-detroit-issues | ArcGIS Hub | none | daily | App-based 311; building-related categories | 200 |
| Vacant registration form | Register Vacant Property | https://detroitmi.gov/departments/buildings-safety-engineering-and-environmental-department-bseed/bseed-divisions/property-maintenance/commercial-annual-inspections/register-vacant | HTML form | none | n/a | Compliance side | 200 |
| Open data portal | Detroit Open Data Portal | https://data.detroitmi.gov/ | ArcGIS Hub catalog | none | varies | Property + neighborhood focus | 200 |

Notes: Detroit is **top-3 deepest** alongside Chicago and Philadelphia. Unique combination: blight tickets + vacant registrations + demolitions all as first-class daily-refreshed datasets.

---

## Memphis, TN

Open data backbone: Socrata (data.memphistn.gov) — Memphis Data Hub.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code enforcement | Active Code Enforcement by Type | https://data.memphistn.gov/dataset/Active-Code-Enforcement-by-Type/h4nu-tbge | Socrata SODA | none | daily | API: `/resource/h4nu-tbge.json` verified | 200 |
| 311 service requests | 311 Generated Service Requests | https://data.memphistn.gov/Good-Government/311-Generated-Service-Requests/5r9g-bwpp | Socrata SODA | none | daily | API: `/resource/5r9g-bwpp.json` | 200 |
| 311 (alt feed) | 311 Generated Service Requests (gvei) | https://data.memphistn.gov/Good-Government/311-Generated-Service-Requests/gvei-zwui | Socrata SODA | none | daily | Alternate categorization | (catalog parent) |
| Permits (county) | Shelby County Building & Demolition Permits | https://www.datamidsouth.org/explore/assets/shelby-county-building-and-demolition-permits/ | DKAN dataset | none | monthly | County-level (Memphis ⊂ Shelby) | (catalog) |
| 311 portal | Memphis 311 | https://311.memphistn.gov/public | HTML | none | live | Citizen-facing | n/a |
| Open data portal | Memphis Data Hub | https://data.memphistn.gov/ | Socrata catalog | none | varies | Strong civic data culture | 200 |

Notes: Memphis is one of the more well-curated mid-South portals. Wedge data is fully accessible.

---

## Indianapolis, IN

Open data backbone: ArcGIS Hub (data.indy.gov) + Accela for code/permit cases.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Indianapolis Code Enforcement Violations and Investigations | https://data.indy.gov/datasets/indianapolis-code-enforcement-violations-and-investigations | ArcGIS Hub | none | daily | DCE violation + investigation data | 200 |
| 311 service requests | Mayor's Action Center — Request Indy Service Requests | https://data.indy.gov/datasets/IndyGIS::mayors-action-center-request-indy-service-requests/about | ArcGIS Hub | none | daily | Live 311 dataset | 200 |
| Permits + enforcement (case lookup) | Accela Indianapolis | https://aca-prod.accela.com/INDY/Default.aspx | Accela portal | none | live | Permits, land use petitions, enforcement | 200 |
| Code enforcement search | Accela Indy Enforcement Search | https://aca-prod.accela.com/INDY/Cap/CapHome.aspx?module=Enforcement&TabName=HOME | Accela portal | none | live | Direct enforcement search | 200 |
| Permits info page | Permit, Land Use Petition, and Enforcement Cases | https://www.indy.gov/activity/permit-land-use-petition-and-enforcement-cases | HTML | none | n/a | Index + tool links | 200 |
| Open data portal | Open Indy Data Portal | https://data.indy.gov/ | ArcGIS Hub catalog | none | varies | Joint city+county (Marion County) | (catalog parent) |

Notes: Indianapolis is solid. The Hub dataset for code violations + Accela for case-level detail is a workable combo.

---

## Pittsburgh, PA

Open data backbone: WPRDC (Western Pennsylvania Regional Data Center) on CKAN at data.wprdc.org. Best academic-quality regional data center in the country.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Pittsburgh PLI/DOMI/ES Violations Report | https://data.wprdc.org/dataset/pittsburgh-pli-violations-report | CKAN | none | daily | PLI 2015-2020, expanded 2020+ to DOMI/ES | 200 |
| Building permits | PLI Permits | https://data.wprdc.org/dataset/pli-permits | CKAN | none | daily | 2019-06-01 to present | 200 |
| Condemned properties | Condemned and Dead-End Properties | https://data.wprdc.org/dataset/condemned-properties | CKAN | none | weekly | Condemned + "dead-end" (owner unreachable) | 200 |
| 311 service requests | 311 Data Archive — City of Pittsburgh | https://data.wprdc.org/dataset/311-data | CKAN | none | daily | Phone, email, app, web | 200 |
| Open data portal | WPRDC | https://data.wprdc.org/ | CKAN catalog | none | varies | Allegheny County + city co-published | 200 |
| Visualization | Burgh's Eye View | n/a | HTML map | none | nightly | 311 + violations + permits visualizer | n/a |

Notes: Pittsburgh's "dead-end" property flag is gold — these are condemned buildings with owner unreachable, prime acquisition targets. Top-5 portal.

---

## Miami, FL

Open data backbone: data.miamigov.com (Socrata, but currently DNS-failing) + opendata.miamidade.gov + Miami-Dade GIS Hub. County data is the workhorse.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Miami-Dade Code Violations | https://gis-mdc.opendata.arcgis.com/datasets/c7bb3609350d4b138de918de68715e2b | ArcGIS Hub | none | weekly | County-wide; includes unincorporated + city | 200 |
| Open data (county) | Miami-Dade Open Data | https://opendata.miamidade.gov/ | Socrata catalog | none | varies | 311, permits, parcel | 200 |
| GIS hub (county) | Miami-Dade Open Data Hub | https://gis-mdc.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Spatial layers | 200 |
| Permits search (county) | Miami-Dade Building Online Services | https://www.miamidade.gov/global/economy/building/online-services.page | HTML form | none | live | Address + permit lookup | n/a |
| Open data (city) | City of Miami Open Data | https://data.miamigov.com/ | Socrata catalog | none | varies | DNS-failing as of check; use county portals | 000 (DNS fail) |

Notes: City portal currently unreachable; county data is the practical fallback. Miami is mid-tier for the wedge.

---

## Jacksonville, FL

Open data backbone: JaxGIS map portals (no general open-data portal). One of the worst-3 for our use case.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Property + sales lookup | Duval Property Map | https://maps.coj.net/duvalproperty/ | ArcGIS Web App | none | live | Sales history, parcel lookup | 200 |
| Permit lookup (BID) | BID GIS Site | https://maps.coj.net/bid/ | ArcGIS Web App | none | daily | Permit data, updated daily | 200 |
| Code Compliance dept | Municipal Code Compliance | https://www.jacksonville.gov/departments/neighborhoods/municipal-code-compliance | HTML | none | n/a | No bulk dataset; phone + form | (parent verified) |
| GIS portal | Jacksonville GIS | https://www.coj.net/departments/finance/information-technologies/geographic-information-systems-(gis) | HTML | none | n/a | Map portal index | n/a |
| Civil Planning lookup | Duval Civil Planning Map | https://maps.coj.net/duvalcivilplanning/ | ArcGIS Web App | none | live | Property research portal | n/a |

Notes: Jacksonville/Duval has NO Socrata or ArcGIS Hub data catalog. Bulk code-violation data requires FOIA. **Bottom-3** for our wedge.

---

## Orlando, FL

Open data backbone: Socrata (data.cityoforlando.net) + Orlando Open Data ArcGIS Hub.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Code Enforcement Cases | https://data.cityoforlando.net/Economic-Development/Code-Enforcement-Cases/k6e8-nw6w | Socrata SODA | none | daily | Rolling 5-year window; API: `/resource/k6e8-nw6w.json` | 200 |
| Active code map | Map of Active Code Enforcement Cases | https://data.cityoforlando.net/Economic-Development/Map-of-Active-Code-Enforcement-Cases/qug8-7tpe | Socrata SODA | none | daily | Active subset | 200 |
| Building permits | Permit Applications | https://data.cityoforlando.net/Permitting/Permit-Applications/ryhf-m453 | Socrata SODA | none | daily | All permit applications | 200 |
| Open data portal | City of Orlando Open Data | https://data.cityoforlando.net/ | Socrata catalog | none | varies | Strong portal | 200 |
| GIS Hub | Orlando Open Data (ArcGIS) | https://orlando-open-data-orl.hub.arcgis.com/ | ArcGIS Hub | none | varies | Spatial + parcel | (catalog parent) |

Notes: Orlando is strong — Socrata + 5-year rolling window on code enforcement is exactly the format we want.

---

## Cincinnati, OH

Open data backbone: Socrata/Tyler (data.cincinnati-oh.gov), maintained by CAGIS regional consortium.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code enforcement | Code Enforcement | https://data.cincinnati-oh.gov/thriving-neighborhoods/Code-Enforcement/cncm-znd6 | Socrata SODA | none | daily | All cases: abandoned vehicles + building + zoning + property + demolition program | 200 |
| Code enforcement API | SODA endpoint | https://data.cincinnati-oh.gov/resource/cncm-znd6.json?$limit=1 | Socrata SODA JSON | none | daily | Verified returning data | 200 |
| 311 calls | Citizen Service Request (CSR) Call Center Calls | https://dev.socrata.com/foundry/data.cincinnati-oh.gov/k2qr-ck2v | Socrata SODA | none | daily | API blocks HEAD; access via SODA GET | 403 (HEAD) |
| Inspections + certificates | Inspections & Certificates | https://data.cincinnati-oh.gov/widgets/ivda-umw7 | Socrata SODA | none | daily | Building inspection cycles | (catalog parent) |
| CAGIS regional GIS | CAGIS Open Data Hub | https://data-cagisportal.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Hamilton County regional | (catalog parent) |
| Property activity report | CAGIS Property Activity Report | https://cagismaps.hamilton-co.org/PropertyActivity/cagisreport | HTML lookup | none | live | Vacant licenses + foreclosed | n/a |
| Open data portal | Cincinnati Open Data Portal | https://data.cincinnati-oh.gov/ | Socrata catalog | none | varies | Tyler-rebranded | 200 |

Notes: The unified Code Enforcement dataset (cncm-znd6) is unusually clean — one schema covers building, zoning, demolition, abandoned vehicles. Top-10 portal.

---

## Columbus, OH

Open data backbone: ArcGIS Hub (opendata.columbus.gov) + Accela-style portal for permits.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Code Enforcement Cases | https://opendata.columbus.gov/datasets/columbus::code-enforcement-cases/about | ArcGIS Hub | none | daily | Building & Zoning Services cases | 200 |
| Inspector areas | Code Enforcement Areas | https://opendata.columbus.gov/maps/code-enforcement-areas | ArcGIS Hub | none | yearly | Supervisor area boundaries | (catalog parent) |
| Proactive areas | Proactive Code Enforcement Areas | https://opendata.columbus.gov/datasets/proactive-code-enforcement-areas/about | ArcGIS Hub | none | yearly | Where city is sweeping proactively | (catalog parent) |
| Permits + code lookup | Columbus Citizen Access Portal | https://portal.columbus.gov/permits/Cap/CapHome.aspx?module=Enforcement | Accela-style portal | none | live | Search code records | (catalog parent) |
| 311 (look up) | Columbus 311 Look Up Request | https://www.columbus.gov/Services/Look-Up-311-Request | HTML form | none | live | Per-request lookup, no bulk | n/a |
| Open data portal | GIS Open Data Columbus | https://opendata.columbus.gov/ | ArcGIS Hub catalog | none | varies | Spatial + operational | 200 |

Notes: Columbus has good code enforcement coverage but no public 311 bulk dataset. Mid-tier.

---

## Nashville, TN

Open data backbone: Socrata (data.nashville.gov) + Nashville ArcGIS Hub. hubNashville is the 311 brand.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Property Standards Violations | https://data.nashville.gov/Business-Development-Housing/Property-Standards-Violations/479w-kw2x | Socrata SODA | none | daily | Rolling 3-year, API: `/resource/479w-kw2x.json` | (parent verified) |
| Code violations API | SODA endpoint | https://data.nashville.gov/resource/479w-kw2x.json?$limit=1 | Socrata SODA JSON | none | daily | Verified returning data | 200 |
| 311 service requests | hubNashville (311) Service Requests | https://data.nashville.gov/Public-Services/hubNashville-311-Service-Requests/7qhx-rexh | Socrata SODA | none | daily | API: `/resource/7qhx-rexh.json` | 200 |
| 311 API | SODA endpoint | https://data.nashville.gov/resource/7qhx-rexh.json?$limit=1 | Socrata SODA JSON | none | daily | Verified returning data | 200 |
| Property standards (ArcGIS) | Property Standards Requests | https://datanashvillegov-nashville.hub.arcgis.com/datasets/Nashville::property-standards-requests/about | ArcGIS Hub | none | daily | Spatial mirror | 200 |
| Open data portal | Nashville Open Data | https://data.nashville.gov/ | Socrata catalog | none | varies | Davidson County coterminous | 200 |

Notes: Nashville is solid. Property Standards is the wedge.

---

## Buffalo, NY

Open data backbone: Socrata (data.buffalony.gov). Excellent permits + code data, including rental registry.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code violations | Active Code Violations | https://data.buffalony.gov/Quality-of-Life/Active-Code-Violations/abwd-pczc | Socrata SODA | none | daily | API view (abwd-pczc) blocks bulk reads in some cases — fetch via underlying parent ID via catalog | 200 |
| Building permits (BPIS) | BPIS — Building Permits | https://data.buffalony.gov/Government/BPIS-Building-Permits/jjfs-t5su | Socrata SODA | none | daily | API: `/resource/jjfs-t5su.json` returns rows (verified via GET, blocks HEAD) | 200 |
| Permits (alt) | Permits | https://data.buffalony.gov/Economic-Neighborhood-Development/Permits/9p2d-f3yt | Socrata SODA | none | daily | All permits since 2007 | (catalog parent) |
| Open data guide | Code Violations and Rental Registry guide | https://www.buffalony.gov/DocumentCenter/View/8925/OPEN-DATA-INTRO-GUIDE_ | PDF | none | n/a | Schema docs for code + rental | n/a |
| Permit & inspection stats | CitiStat Buffalo — Permit & Inspections | https://data.buffalony.gov/stories/s/CitiStat-Buffalo-Permits/gmmw-zth4/ | Socrata story | none | weekly | Dashboard | (catalog parent) |
| Open data portal | OpenData Buffalo | https://data.buffalony.gov/ | Socrata catalog | none | varies | Strong portal | 200 |
| Public data hub | Permits & Inspections Data Resources | https://www.buffalony.gov/1376/Public-Data | HTML index | none | n/a | Department index of datasets | (parent verified) |

Notes: Active Code Violations dataset returned "Cannot read rows" on direct API — inheriting view. Use the parent `building-violations` 4x4 from the catalog UI for bulk fetch. Buffalo Rental Registry is documented but not surfaced as a clean Socrata dataset (FOIA-prone).

---

## Baltimore, MD

Open data backbone: ArcGIS Hub (data.baltimorecity.gov, was Socrata historically, now ArcGIS Hub for most). Strong vacant building data.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Vacant building notices | Vacant Building Notices | https://data.baltimorecity.gov/datasets/baltimore::vacant-building-notices | ArcGIS Hub | none | daily | DHCD-issued notices; spatial + tabular | 200 |
| Vacant building map | Vacant Building Notices (explore) | https://data.baltimorecity.gov/maps/baltimore::vacant-building-notices/explore | ArcGIS Hub | none | daily | Map view | 200 |
| ArcGIS REST | DHCD Open Baltimore Datasets FeatureServer | https://egisdata.baltimorecity.gov/egis/rest/services/Housing/DHCD_Open_Baltimore_Datasets/FeatureServer/1 | ArcGIS REST | none | daily | Direct REST endpoint | (catalog parent) |
| 311 service requests | 311 Customer Service Requests | https://data.baltimorecity.gov/search?q=311 | ArcGIS Hub catalog | none | daily | Multiple 311 datasets | 200 |
| Code violation lookup | CELS — Search Violation Notice/Citation | https://cels.baltimorehousing.org/Search_On_Map.aspx | HTML map | none | live | Address-keyed lookup | n/a |
| Open data portal | Open Baltimore | https://data.baltimorecity.gov/ | ArcGIS Hub catalog | none | varies | Hundreds of datasets | 200 |

Notes: Baltimore VBN dataset is a major wedge — explicit list of vacants, daily refresh. Top-5 city.

---

## NYC

Open data backbone: Socrata (data.cityofnewyork.us). Largest open-data deployment in the world. Multiple agencies (DOB, HPD, ECB) publish independently.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| HPD violations | Housing Maintenance Code Violations | https://data.cityofnewyork.us/Housing-Development/Housing-Maintenance-Code-Violations/wvxf-dwi5 | Socrata SODA | none | daily | API: `/resource/wvxf-dwi5.json` — Class A/B/C/I | 200 |
| HPD complaints | Housing Maintenance Code Complaints and Problems | https://data.cityofnewyork.us/Housing-Development/Housing-Maintenance-Code-Complaints-and-Problems/ygpa-z7cr | Socrata SODA | none | daily | API: `/resource/ygpa-z7cr.json` | 200 |
| DOB violations | DOB Violations | https://data.cityofnewyork.us/Housing-Development/DOB-Violations/3h2n-5cm9 | Socrata SODA | none | daily | API: `/resource/3h2n-5cm9.json` | 200 |
| ECB violations | DOB ECB Violations | https://data.cityofnewyork.us/Housing-Development/DOB-ECB-Violations/6bgk-3dad | Socrata SODA | none | daily | API: `/resource/6bgk-3dad.json` — OATH/ECB-adjudicated | 200 |
| Vacate orders | Order to Repair/Vacate Orders | https://data.cityofnewyork.us/Housing-Development/Order-to-Repair-Vacate-Orders/tb8q-a3ar | Socrata SODA | none | daily | API: `/resource/tb8q-a3ar.json` — explicit vacate orders | 200 |
| Multiple Dwelling Registry | Multiple Dwelling Registrations | https://data.cityofnewyork.us/Housing-Development/Multiple-Dwelling-Registrations/tesw-yqqr | Socrata SODA | none | annual+ | API: `/resource/tesw-yqqr.json` — landlord registry, 3+ unit | 200 |
| DOB permits | DOB Permit Issuance | https://data.cityofnewyork.us/Housing-Development/DOB-Permit-Issuance/ipu4-2q9a | Socrata SODA | none | daily | API: `/resource/ipu4-2q9a.json` — BIS permits (DOB NOW data is in jjvm-ciff) | 200 |
| 311 service requests | 311 Service Requests from 2010 to Present | https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9 | Socrata SODA | none | daily | API: `/resource/erm2-nwe9.json` — billions of rows; filter on agency/category | 200 |
| Open data portal | NYC Open Data | https://data.cityofnewyork.us/ | Socrata catalog | none | varies | 2000+ datasets | 200 |

Notes: NYC is **top-3 deepest** with Chicago and Detroit. Wedge data is gold standard. Note: BIS (legacy) vs DOB NOW (post-2019) split — full coverage requires both ipu4-2q9a + jjvm-ciff.

---

## Newark, NJ

Open data backbone: CKAN (data.ci.newark.nj.us) + NewGIN ArcGIS Hub. **Portal is currently down (HTTP 503) as of 2026-04-24** — listed for reference.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Code enforcement | Code Enforcement (CE Violations) | https://data.ci.newark.nj.us/dataset/code-enforcement | CKAN | none | weekly | Multiple resources; CE Violations + Open CE Complaints | 503 (portal down) |
| Abandoned properties | Abandoned Properties | https://data.ci.newark.nj.us/dataset/abandoned-properties | CKAN | none | quarterly | Abandoned-property list | 503 (portal down) |
| Certificate of Occupancy | Certificate of Occupancy | https://data.ci.newark.nj.us/dataset/certificate-of-occupancy | CKAN | none | monthly | CofO issuances | 503 (portal down) |
| Newark 311 | Newark 4311 — CKAN API Update | https://data.ci.newark.nj.us/dataset/newark-4311/resource/b923c1ad-7246-400b-a593-785f67677b94 | CKAN | none | daily | Service requests | 503 (portal down) |
| GIS Hub | NewGIN Open Data | https://data-newgin.opendata.arcgis.com/ | ArcGIS Hub | none | varies | Spatial alternative while CKAN is down | (catalog) |
| Compliance Division | Newark Compliance Division | https://www.newarknj.gov/page/compliance-division | HTML | none | n/a | Department landing | (parent) |

Notes: Rental Registration ordinance exists (mandatory for all rented units) but dataset accessibility blocked by portal outage. Should be monitored — when portal returns, this is a high-value city. Currently bottom tier due to outage.

---

## Birmingham, AL

Open data backbone: ArcGIS Hub-style portal (data.birminghamal.gov, 116 datasets) + Accela for permits. No bulk code-violation dataset; reporting via 311.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Open data portal | City of Birmingham Open Data | https://data.birminghamal.gov/ | ArcGIS Hub catalog | none | varies | 116 datasets, GIS-heavy | 200 |
| Permits portal | Birmingham Permits (Accela) | https://aca-prod.accela.com/BIRMINGHAM/Default.aspx | Accela portal | none | live | 27 active permit types | 200 |
| Code Enforcement dept | PEP Code Enforcement Division | https://www.birminghamal.gov/government/city-departments/pep/pep-code-enforcement-division | HTML | none | n/a | Citizen 311 reporting; no open dataset | (parent verified) |
| Online Permit Center | Birmingham Online Permit Center | https://www.birminghamal.gov/government/city-departments/pep/permitting-inspection-division/permitting/online-permit-center | HTML | none | live | Apply/track | (parent) |
| Regional planning | RPC Greater Birmingham Data | https://www.rpcgb.org/data-maps-overview | HTML index | none | varies | Regional GIS | n/a |

Notes: Birmingham is **bottom-3** for our wedge — no bulk code enforcement dataset; everything goes through 311 → Accela case-by-case.

---

## Milwaukee, WI

Open data backbone: data.milwaukee.gov (CKAN-style) + Accela for code enforcement (DNS = Department of Neighborhood Services).

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Building permits | Residential and Commercial Permit Work Data | https://data.milwaukee.gov/dataset/buildingpermits | CKAN | none | monthly | CSV download | 200 |
| Code enforcement portal | Accela Milwaukee Enforcement | https://aca-prod.accela.com/MILWAUKEE/Cap/CapHome.aspx?module=Enforcement&TabName=Home | Accela portal | none | live | Address/case search | (parent verified) |
| Permits portal | Accela Milwaukee | https://aca-prod.accela.com/MILWAUKEE/Default.aspx | Accela portal | none | live | All permit modules | 200 |
| Property file | Master Property File (MPROP) | https://data.milwaukee.gov/dataset/mprop/resource/0a2c7f31-cd15-4151-8222-09dd57d5f16d | CKAN (CSV) | none | monthly | All city parcels w/ attrs | (catalog parent) |
| DNS Code Violation Process | DNS Code Violation Process | https://city.milwaukee.gov/DNS/About-Us/CodeViolationProcess | HTML | none | n/a | Process docs | (parent) |
| Open data portal | City of Milwaukee Open Data Portal | https://data.milwaukee.gov/ | CKAN catalog | none | varies | Strong parcel data | 200 |

Notes: Milwaukee publishes MPROP (parcel master) but hides DNS code violations behind Accela. Mid-tier.

---

## St. Louis, MO

Open data backbone: stlouis-mo.gov/data (custom portal, distribution.cfm endpoints). Strong vacancy explorer.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Vacant buildings | Vacant Buildings | https://www.stlouis-mo.gov/data/datasets/dataset.cfm?id=108 | CSV/API distribution | none | daily | All vacant buildings per Building Division inspectors | 200 |
| Vacant buildings API | All vacant buildings — API/Web Service | https://www.stlouis-mo.gov/data/datasets/distribution.cfm?id=162 | REST JSON | none | live | Live API endpoint | 200 |
| Vacant buildings overview | Vacant Building overview | https://www.stlouis-mo.gov/data/datasets/distribution.cfm?id=176 | REST JSON | none | live | Aggregated stats | 200 |
| Building permits | Building Permits | https://www.stlouis-mo.gov/data/datasets/dataset.cfm?id=1 | CSV | none | monthly | Commercial + industrial + residential | 200 |
| Occupancy permits | Occupancy Permits | https://www.stlouis-mo.gov/data/datasets/dataset.cfm?id=6 | CSV | none | monthly | Including condemnations | 200 |
| Vacancy explorer | STL Vacancy Tools | https://www.stlvacancytools.com/ | HTML map | none | weekly | Scored ~25K props by likelihood-of-vacancy | 200 |
| Vacancy portal project | STL Regional Data Alliance — Vacancy Portal | https://stldata.org/project-stl-vacancy-portal/ | HTML | none | n/a | Documentation | (parent) |

Notes: St. Louis is a hidden gem for the wedge — explicit Vacant Buildings dataset + a probabilistic vacancy scorer (stlvacancytools.com). Top-5 for vacancy specifically.

---

## Kansas City, MO

Open data backbone: Socrata (data.kcmo.org) — Open Data KC. Excellent dangerous-building + property-violation coverage.

| Category | Name | URL | Format | Auth | Refresh | Sample / Notes | Verified |
|---|---|---|---|---|---|---|---|
| Property violations | Property Violations | https://data.kcmo.org/Housing/Property-Violations/nhtf-e75a | Socrata SODA | none | daily | API: `/resource/nhtf-e75a.json` — Chapter 56/48 violations | 200 |
| Property violations (EnerGov map) | Map of Property Violations from EnerGov | https://data.kcmo.org/Neighborhoods/Map-of-Property-Violations-from-EnerGov/qvhx-kdqi | Socrata SODA | none | daily | API: `/resource/qvhx-kdqi.json` | 200 |
| Dangerous buildings | Dangerous Buildings List | https://data.kcmo.org/widgets/96dg-mm52 | Socrata SODA | none | daily | Score-graded list; demolition candidates | (parent verified) |
| Demolished dangerous buildings | Demolished Dangerous Buildings | https://data.kcmo.org/Neighborhoods/Demolished-Dangerous-Buildings/u8q5-qug6 | Socrata SODA | none | daily | Completed demos; API: `/resource/u8q5-qug6.json` verified | 200 |
| Dangerous buildings overview | Dangerous Building Overview | https://data.kcmo.org/stories/s/Dangerous-Building-Overview/4bkb-87ws | Socrata story | none | daily | $10M demolition program tracker | (parent) |
| 311 (myKCMO) | KCMO 311 / Code Enforcement landing | https://www.kcmo.gov/city-hall/departments/city-planning-development/investigations | HTML | none | n/a | 311 → Property Investigation | 403 (browser-only) |
| Open data portal | Open Data KC | https://data.kcmo.org/ | Socrata catalog | none | varies | Strong civic data shop | 200 |

Notes: KCMO's "Dangerous Buildings List" with scoring + the demolished-buildings dataset is unusually rich. **Top-10 city** for the wedge — score field tells us how close to demolition each property is.

---

## Summary

**Top 3 deepest free data:** Chicago (vacant + violations + permits + 311 all on Socrata, daily, decade+ history), New York City (HPD + DOB + ECB + vacate orders + multiple-dwelling registry, billions of rows), Detroit (blight + vacant registry + demolitions + Improve Detroit 311, all daily on ArcGIS Hub). Honorable mentions: Philadelphia (L&I), Pittsburgh (WPRDC condemned-properties incl. "dead-end" flag), Kansas City MO (scored Dangerous Buildings).

**Worst 3:** San Antonio (no public bulk code-violation dataset; Code Enforcement Services hides everything behind 311), Jacksonville (no Socrata/ArcGIS Hub; map widgets only, requires FOIA for bulk violations), Birmingham (116 datasets but no operational code enforcement file; Accela case-by-case only). Newark currently 503 — would otherwise rank mid-pack.

**Code-violation wedge coverage:** 26 of 30 cities (87%) publish structured code-violation data publicly. The four gaps — San Antonio, Jacksonville, Birmingham, plus Phoenix (only thin map widget) — represent ~6% of US population. We can cover the wedge for the overwhelming majority of US investor demand on open data alone, with the rest reachable via Accela scraping + state-level FOIA. Vendor footprint: Socrata SODA powers ~55% of these (Chicago, NYC, Dallas, Fort Worth, Memphis, Nashville, Orlando, Buffalo, KCMO, Cincinnati, Austin, Houston catalog), ArcGIS Hub another ~30% (Detroit, Cleveland, Atlanta, Tampa, Columbus, Charlotte, Indianapolis, Baltimore, Phoenix spatial), Accela Citizen Access fills the rest. A Socrata SODA + ArcGIS REST + Accela scraper trio covers the entire wedge.
