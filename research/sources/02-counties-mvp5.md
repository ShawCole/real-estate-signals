# MVP-5 Counties — Public Data Source Inventory

Exhaustive enumeration of free / public real-estate data sources for the 5 MVP counties: Harris TX, Cuyahoga OH, Philadelphia PA, Cook IL, Maricopa AZ.

Verification: every URL below was probed with `curl -sIL --max-time 10` on 2026-04-24 and returned a 2xx, 3xx, or known-good 403/405 (server blocks HEAD but the resource is browser-reachable). Rows whose URLs returned 000/404/500 were dropped.

Conventions:
- **Format**: `ArcGIS REST` (MapServer / FeatureServer JSON), `ArcGIS Hub` (download landing), `Socrata SODA` (data.json + 4x4 ID), `REST JSON`, `HTML form`, `CSV download`, `PDF only`, `RSS`.
- **Auth**: `none` = no login; `account` = free registration required; `paywall` = subscription/per-doc fee.
- **Refresh**: copied from the publisher's own metadata when stated; `unknown` otherwise.

---

## Harris County, TX (FIPS 48201)

Vendor stack: HCAD (custom assessor on its own subdomain) + Tyler `i2File` for the District Clerk + bespoke `cclerk.hctx.net` ASP.NET app for the County Clerk + Esri ArcGIS Server hosted at `gis.hctx.net` for parcel geometry. Tax sales are run in-person by 8 Constables (no RealAuction). Strongest free exposure of the five: full CAMA roll downloadable, parcel polygons via REST, court dockets searchable without login.

| Category | Name | URL | Format | Auth | Cost | Refresh | Sample data | Verified |
|---|---|---|---|---|---|---|---|---|
| Assessor portal | Harris Central Appraisal District (HCAD) main | https://hcad.org/ | HTML form | none | free | annual | landing for property search | 200 |
| Assessor portal | HCAD Online Services hub | https://hcad.org/hcad-online-services/ | HTML form | none | free | unknown | links to property search, GIS, pdata | 200 |
| Assessor portal | HCAD Property Tax Database | https://hcad.org/hcad-online-services/property-tax-database | HTML form | none | free | annual | parcel-level taxing units + rates | 200 |
| Assessor portal | HCAD Public Search subdomain | https://public.hcad.org/ | HTML form | none | free | daily | account lookup by address/owner/acct | 200 |
| Assessor bulk | HCAD Public Data download index | https://hcad.org/hcad-online-services/pdata/ | CSV download | account | free | annual | full county CAMA tab-delimited | 200 |
| Assessor bulk | HCAD GIS Shapefile downloads | https://hcad.org/pdata/pdata-gis-downloads.html | CSV download | account | free | quarterly | parcel polygons SHP | 200 |
| Assessor bulk | HCAD download server | https://download.hcad.org/ | CSV download | account | free | annual | direct file fetch (sample/2024 paths return 500 outside session) | 200 |
| Assessor REST | HCAD Parcels MapServer | https://www.gis.hctx.net/arcgis/rest/services/HCAD/Parcels/MapServer | ArcGIS REST | none | free | quarterly | parcel polygon, owner_name_1 | 200 |
| Assessor REST | HCAD Parcels Layer 0 (queryable) | https://www.gis.hctx.net/arcgis/rest/services/HCAD/Parcels/MapServer/0 | ArcGIS REST | none | free | quarterly | full parcel attribute table | 200 |
| Assessor REST | HCAD MUD MapServer | https://www.gis.hctx.net/arcgis/rest/services/HCAD/HCAD_MUD/MapServer | ArcGIS REST | none | free | quarterly | municipal utility district overlays | 200 |
| Assessor REST | HCAD Subdivisions MapServer | https://www.gis.hctx.net/arcgishcpid/rest/services/HCAD/HCAD_Subdivisions/MapServer | ArcGIS REST | none | free | quarterly | subdivision polygons | 200 |
| Assessor REST | gis.hctx.net root services directory | https://www.gis.hctx.net/arcgis/rest/services | ArcGIS REST | none | free | rolling | full HCAD service tree | 200 |
| Treasurer | Harris County Tax Office property tax search | https://www.hctax.net/Property/PropertyTax | HTML form | none | free | daily | tax bill, paid/unpaid | 200 |
| Treasurer | Harris County Tax Sales index | https://www.hctax.net/Property/TaxSales | HTML form | none | free | monthly | sale calendar + rules | 200 |
| Treasurer | Harris County tax sale property listing | https://www.hctax.net/Property/listings/taxsalelisting | HTML form | none | free | monthly | per-Constable struck-off list | 200 |
| Recorder | Harris County Clerk Real Property search | https://www.cclerk.hctx.net/applications/websearch/RP.aspx | HTML form | none | free | daily | deed/mortgage index back to 1960s | 200 |
| Recorder | Harris County Clerk Foreclosure notices | https://www.cclerk.hctx.net/applications/websearch/FRCL_R.aspx | HTML form | none | free | daily | non-judicial foreclosure notices | 200 |
| Recorder | Harris County Clerk Public Records hub | https://www.cclerk.hctx.net/PublicRecords.aspx | HTML form | none | free | daily | links to all clerk search apps | 200 |
| Recorder | Harris County Clerk Real Property landing | https://www.cclerk.hctx.net/RealProperty.aspx | HTML form | none | free | daily | recording fee + e-record info | 200 |
| Probate | Harris County Clerk Probate Court search | https://www.cclerk.hctx.net/applications/websearch/Probate.aspx | HTML form | none | free | daily | probate filings, 4 statutory probate courts | 200 |
| Civil court | Harris County District Clerk eDocs search | https://www.hcdistrictclerk.com/edocs/public/search.aspx | HTML form | account | free | daily | civil/family/foreclosure dockets | 200 |
| Civil court | Harris County District Clerk court-calendar lookup | https://www.hcdistrictclerk.com/Common/CourtCalendars/CourtCalendarDocketSearch.aspx | HTML form | none | free | daily | per-court daily docket | 200 |
| JP/eviction | Harris County JP Find My Case | https://jpwebsite.harriscountytx.gov/FindMyCase/search.jsp | HTML form | none | free | daily | small claims, evictions across 16 JP precincts | 200 |
| JP/eviction | Harris County JP Public Data Extract | https://jpwebsite.harriscountytx.gov/PublicExtracts/search.jsp | CSV download | none | free | daily | bulk eviction-case extracts | 200 |
| County GIS | Harris County Open Data portal (ArcGIS Hub) | https://geo-harriscounty.opendata.arcgis.com/ | ArcGIS Hub | none | free | rolling | parcels, zoning, ortho, addresses | 200 |
| County GIS | HCAD Boundary on ArcGIS Hub | https://hub.arcgis.com/datasets/HarrisCounty::hcad-harris-county-boundary/about | ArcGIS Hub | none | free | annual | county boundary | 200 |
| Sheriff sales | Harris County Sheriff Civil sales | https://www.harriscountysheriff.org/civil/sales | HTML form | none | free | weekly | execution / writ-of-possession sales | 200 |
| Tax-sale info | County Tax Sale App (CTSA, statewide) | https://countytaxsaleapp.org/ | HTML form | account | free | monthly | TX Constable struck-off properties | 200 |
| Foreclosure list | Houston Foreclosure Listing Service free tax list | https://www.foreclosehouston.com/products/free-tax-list | HTML form | none | free | monthly | aggregated tax-sale roster | 200 |

---

## Cuyahoga County, OH (FIPS 39035)

Vendor stack: ArcGIS Hub for GIS + custom MyPlace for assessor + PublicSearch.us (Tyler) for recorder + Cuyahoga's own `cpdocket` ASP.NET for Common Pleas Court + RealAuction for sheriff sales (online-only since 2021). Probate is on a separate subdomain. Forfeited Land program is run by the Fiscal Officer; Land Bank is an independent 501(c)(3).

| Category | Name | URL | Format | Auth | Cost | Refresh | Sample data | Verified |
|---|---|---|---|---|---|---|---|---|
| Assessor portal | Fiscal Officer landing | https://cuyahogacounty.gov/fiscal-officer | HTML form | none | free | unknown | departmental landing | 200 |
| Assessor portal | MyPlace property search | https://myplace.cuyahogacounty.gov/ | HTML form | none | free | daily | parcel, owner, sales, value, photos | 200 |
| Recorder | Cuyahoga Recorder PublicSearch (Tyler) | https://cuyahoga.oh.publicsearch.us/ | HTML form | none | free | daily | deeds, mortgages, liens, lis pendens 1810- | 200 |
| Recorder | Recorded Documents and Property Deeds (Fiscal Office) | https://cuyahogacounty.gov/fiscal-officer/departments/recorded-documents | HTML form | none | free | daily | landing for recording office | 200 |
| County GIS | CuyahogaGIS Hub (geospatial) | https://geospatial.gis.cuyahogacounty.gov/ | ArcGIS Hub | none | free | rolling | parcels, ortho, zoning | 200 |
| County GIS | CuyahogaGIS Hub Data page | https://geospatial.gis.cuyahogacounty.gov/pages/data | ArcGIS Hub | none | free | rolling | dataset list with download links | 200 |
| County GIS | Cuyahoga County Open Data | https://data-cuyahoga.opendata.arcgis.com/ | ArcGIS Hub | none | free | rolling | second open-data portal | 200 |
| County GIS | Fiscal GIS Hub | https://fiscalhub.gis.cuyahogacounty.gov/ | ArcGIS Hub | none | free | rolling | parcel-themed hub | 200 |
| Treasurer | Treasury landing | https://cuyahogacounty.gov/treasury | HTML form | none | free | daily | tax bill payment portal | 200 |
| Treasurer | Delinquent tax payment plans | https://cuyahogacounty.gov/treasury/pay-your-taxes/delinquent-tax-payment-plan | HTML form | none | free | unknown | DEL tax program rules | 200 |
| Tax-lien sale | Cuyahoga tax lien certificate sales (Treasury) | https://cuyahogacounty.gov/treasury/delinquency/tax-lien-certificate-sales | HTML form | none | free | annual | bulk negotiated TLC sale info | 200 |
| Forfeited land | Forfeited Lands (Fiscal Officer) | https://cuyahogacounty.gov/fiscal-officer/departments/real-property/forfeited-lands | HTML form | none | free | annual | Sept Forfeited Land Sale list | 200 |
| Sheriff sales | Sheriff department landing | https://cuyahogacounty.gov/sheriff | HTML form | none | free | weekly | dept landing | 200 |
| Sheriff sales | Sheriff Civil Foreclosure Sales page | https://cuyahogacounty.gov/sheriff/civil/foreclosure-sales | HTML form | none | free | weekly | Monday 9am online auction info | 200 |
| Sheriff sales | Cuyahoga Sheriff Sale (RealAuction) | https://cuyahoga.sheriffsaleauction.ohio.gov/ | HTML form | account | free | weekly | live foreclosure auction listings | 403 (browser-reachable) |
| Foreclosure court | Common Pleas docket search | https://cpdocket.cp.cuyahogacounty.gov/ | HTML form | none | free | daily | civil case search incl. foreclosure | 200 |
| Foreclosure court | Common Pleas Search.aspx | https://cpdocket.cp.cuyahogacounty.gov/Search.aspx | HTML form | none | free | daily | typed search interface | 200 |
| Foreclosure court | Common Pleas Sheriff Sales search | https://cpdocket.cp.cuyahogacounty.gov/sheriffsearch/search.aspx | HTML form | none | free | daily | per-case sheriff sale results | 200 |
| Foreclosure court | Court resource center on foreclosure | https://cp.cuyahogacounty.gov/court-resources/foreclosure/ | HTML form | none | free | unknown | overview of mediation programs | 200 |
| Probate | Probate Court landing | https://probate.cuyahogacounty.gov/ | HTML form | none | free | daily | docket landing | 200 |
| Probate | Probate web docket (`/pa/`) | https://probate.cuyahogacounty.gov/pa/ | HTML form | none | free | daily | estate filing search | 200 |
| Probate | Probate public-records page | https://probate.cuyahogacounty.gov/publicrecords.aspx | HTML form | none | free | daily | record-request portal | 200 |
| Probate | Probate Public Records Search hub | https://probate.cuyahogacounty.gov/Public-Records-Search | HTML form | none | free | daily | aggregated public-record links | 200 |
| Probate | Probate eFiling | https://efiling.probate.cuyahogacounty.gov/ | HTML form | account | free | live | e-file decedent estates | 200 |
| Probate | Probate case search | https://probate.cuyahogacounty.gov/case-search | HTML form | none | free | daily | name + case-type lookup | 200 |
| Land bank | Cuyahoga Land Bank | https://cuyahogalandbank.org/ | HTML form | none | free | weekly | demolition + side-yard programs | 200 |
| Demolition | County Code chap. 807 (Demolition Program) | https://cuyahogacounty.gov/code/titles/title-8-economic-development-and-regional-collaboration/chapter-807-property-demolition-program | HTML form | none | free | annual | demolition fund grant rules | 200 |
| UCC | Ohio SOS UCC Filing Portal (statewide) | https://ucc.ohiosos.gov/ | HTML form | account | free | daily | UCC1/3/5 search by debtor | 403 (browser-reachable) |

---

## Philadelphia, PA (FIPS 42101)

Vendor stack: city-built CityGeo stack — Atlas (parcel viewer) + Carto SQL API (the workhorse OPA endpoint) + ArcGIS Online org `phl.maps.arcgis.com` for hosted feature services + OpenDataPhilly catalog (CKAN). Sheriff sales run on Bid4Assets. Register of Wills (probate) is the **only major hole** — no online docket. Civil dockets are on the FJD's `fjdefile` portal.

| Category | Name | URL | Format | Auth | Cost | Refresh | Sample data | Verified |
|---|---|---|---|---|---|---|---|---|
| Assessor portal | OPA / Property search | https://property.phila.gov/ | HTML form | none | free | nightly | per-parcel value, owner, sales, photos | 200 |
| Assessor portal | Atlas address explorer | https://atlas.phila.gov/ | HTML form | none | free | nightly | unified address lookup | 200 |
| Assessor portal | Atlas property tab (deep link) | https://atlas.phila.gov/property | HTML form | none | free | nightly | direct property pane | 200 |
| Assessor REST | OPA Properties Public FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/OPA_Properties_Public/FeatureServer | ArcGIS REST | none | free | nightly | full assessment roll | 200 |
| Assessor REST | DOR Parcel boundary FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/DOR_Parcel/FeatureServer | ArcGIS REST | none | free | weekly | DOR-recorded parcel polygons | 200 |
| Assessor REST | PHL_LANDPARCEL FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/PHL_LANDPARCEL/FeatureServer | ArcGIS REST | none | free | weekly | alternate landparcel layer | 200 |
| Assessor REST | Carto SQL API (opa_properties_public) | https://phl.carto.com/api/v2/sql?q=SELECT+market_value+FROM+opa_properties_public+LIMIT+1 | REST JSON | none | free | nightly | full SQL on assessment data | 200 |
| Recorder | Department of Records / PhilaDox dataset | https://opendataphilly.org/datasets/philadox-property-documents/ | CSV download | none | free | nightly | recorded docs since 1999 | 200 |
| Recorder | Real Estate Transfers dataset | https://opendataphilly.org/datasets/real-estate-transfers/ | CSV download | none | free | nightly | RTT_SUMMARY all years | 200 |
| Recorder | RTT_Summary FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/RTT_Summary/FeatureServer | ArcGIS REST | none | free | nightly | realty-transfer-tax records | 200 |
| Recorder | Carto SQL on rtt_summary | https://phl.carto.com/api/v2/sql?q=SELECT+document_type+FROM+rtt_summary+LIMIT+1 | REST JSON | none | free | nightly | live SQL on transfers | 200 |
| Recorder | Sheriff Office Recorded Deeds page | https://phillysheriff.com/recorded-deeds/ | HTML form | none | free | weekly | sheriff-deed indexing | 200 |
| Treasurer (Revenue) | Real Estate Tax Balances dataset | https://opendataphilly.org/datasets/real-estate-tax-balances/ | CSV download | none | free | weekly | per-parcel principal+interest+penalty | 200 |
| Treasurer | Real Estate Tax Delinquencies on PHLmaps | https://data-phl.opendata.arcgis.com/datasets/phl::real-estate-tax-delinquencies/about | ArcGIS Hub | none | free | weekly | downloadable delinquency list | 200 |
| Civil court | First Judicial District civil docket access | https://fjdefile.phila.gov/efsfjd/zk_fjd_public_qry_03.zp_dktrpt_frames | HTML form | none | free | daily | civil docket reports | 200 |
| Civil court | Philadelphia Courts portal | https://www.courts.phila.gov/ | HTML form | none | free | daily | court directory | 200 |
| Civil court | PA UJS Portal (statewide case search) | https://ujsportal.pacourts.us/CaseSearch | HTML form | none | free | daily | mortgage foreclosure dockets statewide | 405 (POST-only landing, browser-reachable) |
| Sheriff sales | Sheriff & lien sales process | https://phillysheriff.com/ | HTML form | none | free | monthly | mortgage + tax sale calendar | 200 |
| Sheriff sales | Bid4Assets Philadelphia | https://www.bid4assets.com/PhiladelphiaSheriffSales | HTML form | account | free | monthly | live online sheriff auction | 403 (browser-reachable) |
| Sheriff sales | Sheriff_Sale_Properties FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/Sheriff_Sale_Properties/FeatureServer | ArcGIS REST | none | free | monthly | upcoming sale parcels | 200 |
| Code enforcement | L&I main portal | https://li.phila.gov/ | HTML form | none | free | daily | permits, violations, licenses | 200 |
| Code enforcement | L&I Property History tool | https://li.phila.gov/property-history | HTML form | none | free | daily | per-address violation timeline | 200 |
| Code enforcement | LI Code Violations FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/LI_Code_Violations/FeatureServer | ArcGIS REST | none | free | nightly | full violation export | 200 |
| Code enforcement | OpenDataPhilly LI Code Violations | https://opendataphilly.org/datasets/licenses-and-inspections-code-violations/ | CSV download | none | free | nightly | dataset catalog entry | 200 |
| Vacant property | Vacant Indicators Building FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/Vacant_Indicators_Bldg/FeatureServer | ArcGIS REST | none | free | weekly | likely-vacant building polygons | 200 |
| Vacant property | Vacant Indicators Land FeatureServer | https://services.arcgis.com/fLeGjb7u4uXqeF9q/arcgis/rest/services/Vacant_Indicators_Land/FeatureServer | ArcGIS REST | none | free | weekly | likely-vacant lot polygons | 200 |
| County GIS | OpenDataPhilly catalog | https://opendataphilly.org/datasets/ | ArcGIS Hub | none | free | rolling | full citywide catalog | 200 |
| County GIS | data-phl.opendata.arcgis.com | https://data-phl.opendata.arcgis.com/datasets/phl::opa-properties-public/explore | ArcGIS Hub | none | free | nightly | OPA explorer | 200 |
| County GIS | phl.maps.arcgis.com (CityGeo AGO org) | https://phl.maps.arcgis.com/ | ArcGIS Hub | none | free | rolling | hosted maps + items | 200 |
| Probate | Register of Wills landing | https://www.phila.gov/departments/register-of-wills/ | HTML form | none | free | unknown | dept landing — no online docket | 403 (browser-reachable) |

---

## Cook County, IL (FIPS 17031)

Vendor stack: ArcGIS Hub `hub-cookcountyil` + Socrata `datacatalog.cookcountyil.gov` for assessor open data + `cookcountyclerkofcourt.org` for civil/probate dockets + Treasurer's bespoke ASP.NET app + `cooktaxsale.com` (RealAuction for the Annual Tax Sale). Recorder of Deeds was folded into the Clerk in Dec 2020.

| Category | Name | URL | Format | Auth | Cost | Refresh | Sample data | Verified |
|---|---|---|---|---|---|---|---|---|
| Assessor portal | Cook County Assessor's Office | https://www.cookcountyassessoril.gov/ | HTML form | none | free | weekly | parcel, value, photos, appeals | 403 (browser-reachable) |
| Assessor portal | Cook County Property Tax Portal | https://www.cookcountypropertyinfo.com/ | HTML form | none | free | daily | aggregator across Treasurer/Assessor/BOR | 200 |
| Assessor portal | Assessor map advanced | https://assessorpropertydetails.cookcountyil.gov/maps/mapadv.aspx | HTML form | none | free | weekly | spatial parcel lookup | 200 |
| Assessor portal | CookViewer mapping app | https://maps.cookcountyil.gov/cookviewer/ | HTML form | none | free | weekly | unified parcel viewer | 200 |
| Assessor bulk | Socrata data catalog | https://datacatalog.cookcountyil.gov/ | Socrata SODA | none | free | rolling | open-data root | 200 |
| Assessor bulk | Assessor Residential Assessment Model (bcnq-qi2z) | https://datacatalog.cookcountyil.gov/Property-Taxation/Cook-County-Assessor-s-Residential-Assessment-Mode/bcnq-qi2z | Socrata SODA | none | free | annual | residential AVM dataset | 200 |
| Assessor bulk | Parcel Universe (nj4t-kc8j) | https://datacatalog.cookcountyil.gov/Property-Taxation/Assessor-Parcel-Universe-Current-Assessment-Cycle-/nj4t-kc8j | Socrata SODA | none | free | annual | full parcel roll | 200 |
| Assessor bulk | Parcel Universe SODA endpoint | https://datacatalog.cookcountyil.gov/resource/nj4t-kc8j.json | Socrata SODA | none | free | annual | live JSON query | 200 |
| Assessor bulk | Residential AVM SODA endpoint | https://datacatalog.cookcountyil.gov/resource/bcnq-qi2z.json | Socrata SODA | none | free | annual | live JSON query | 200 |
| Assessor bulk | Assessor Sales (wvhk-k5uv) | https://datacatalog.cookcountyil.gov/Property-Taxation/Cook-County-Assessor-s-Sales/wvhk-k5uv | Socrata SODA | none | free | annual | qualified arms-length sales | 200 |
| County GIS | Cook Central (ArcGIS Hub) | https://hub-cookcountyil.opendata.arcgis.com/ | ArcGIS Hub | none | free | rolling | central GIS portal | 200 |
| County GIS | Property Open Data page | https://hub-cookcountyil.opendata.arcgis.com/pages/property-open-data | ArcGIS Hub | none | free | rolling | curated property layers | 200 |
| County GIS | Parcels 2024 dataset on Hub | https://hub-cookcountyil.opendata.arcgis.com/datasets/parcels-2024 | ArcGIS Hub | none | free | annual | parcel polygons | 200 |
| County GIS | Parcel FeatureServer (U35BQTmVA2MAFFlt) | https://services3.arcgis.com/U35BQTmVA2MAFFlt/arcgis/rest/services/Parcel/FeatureServer | ArcGIS REST | none | free | annual | parcel polygons (FS) | 200 |
| County GIS | CookCounty AGO services (q4ItLDuiZGxZyUVB) | https://services2.arcgis.com/q4ItLDuiZGxZyUVB/arcgis/rest/services | ArcGIS REST | none | free | rolling | county hosted services | 200 |
| County GIS | CookCounty AGO services (ZFFvX3wU3vmhdEf2) | https://services1.arcgis.com/ZFFvX3wU3vmhdEf2/arcgis/rest/services | ArcGIS REST | none | free | rolling | county hosted services (alt org) | 200 |
| Recorder | Clerk Recordings search | https://www.cookcountyclerkil.gov/recordings/search-recordings | HTML form | none | free | daily | deed/mortgage index | 200 |
| Recorder | Clerk Recordings landing | https://www.cookcountyclerkil.gov/recordings | HTML form | none | free | daily | recordings dept landing | 200 |
| Recorder | Property Fraud Unit (free alert signup) | https://www.cookcountyclerkil.gov/recordings/property-fraud-unit | HTML form | account | free | daily | recording-alert email/SMS | 200 |
| Treasurer / tax sale | Tax Sale info (property tax portal) | https://cookcountypropertyinfo.com/taxsale.aspx | HTML form | none | free | annual | tax-sale schedule | 200 |
| Treasurer / tax sale | Cook County Tax Sale (RealAuction) | https://www.cooktaxsale.com | HTML form | account | free | annual | bidder portal for Annual Tax Sale | 200 |
| Treasurer / tax sale | Clerk delinquent tax search | https://www.cookcountyclerkil.gov/property-taxes/delinquent-property-tax-search | HTML form | none | free | daily | scavenger sale info + delinquencies | 200 |
| Land bank | Cook County Land Bank Authority | https://cookcountylandbank.org/ | HTML form | none | free | weekly | tax-cert + acquisition program | 200 |
| Civil/foreclosure court | Clerk of the Circuit Court | https://www.cookcountyclerkofcourt.org/ | HTML form | none | free | daily | clerk landing | 200 |
| Civil/foreclosure court | On-line Case Information | https://www.cookcountyclerkofcourt.org/online-case-information | HTML form | none | free | daily | civil/chancery/probate/eviction lookups | 200 |
| Civil/foreclosure court | Case Look-up portal | https://www.cookcountyclerkofcourt.org/case-look | HTML form | none | free | daily | unified search | 200 |
| Foreclosure court | Mortgage Foreclosure Section (Chancery) | https://www.cookcountycourtil.gov/division/chancery/mortgage-foreclosure-section | HTML form | none | free | daily | division landing | 200 |
| Foreclosure court | Foreclosure case-type page | https://www.cookcountycourtil.gov/case-type/foreclosure | HTML form | none | free | daily | filing + procedure overview | 200 |
| Eviction court | Evictions (Forcible Entry & Detainer) | https://www.cookcountycourtil.gov/case-type/evictions-forcible-entry-detainer | HTML form | none | free | daily | per-municipal-district eviction info | 200 |
| Probate | Probate Division | https://www.cookcountyclerkofcourt.org/divisions/probate-division | HTML form | none | free | daily | estate / guardianship dockets | 200 |
| Sheriff | Cook County Sheriff (state) | https://www.cookcountysheriff.org/ | HTML form | none | free | daily | dept landing (foreclosure-sale calendar inside) | 200 |
| Sheriff | Cook County Sheriff (canonical) | https://cookcountysheriffil.gov/ | HTML form | none | free | daily | mirror domain | 200 |
| Sheriff | Sheriff eviction procedure guide | https://cookcountysheriffil.gov/evictions/eviction-procedure-plaintiffs-guide/ | HTML form | none | free | unknown | eviction execution rules | 200 |

---

## Maricopa County, AZ (FIPS 04013)

Vendor stack: bespoke MCAssessor stack (`mcassessor.maricopa.gov` + `gis.mcassessor.maricopa.gov` Esri Server) + `recorder.maricopa.gov` (custom; HEAD blocked but browser-reachable) + Treasurer ASP.NET portal + RealAuction `maricopa.arizonataxsale.com` for the annual tax-lien sale + AGO org `data-maricopa.opendata.arcgis.com` for hub-style downloads. Notably: **Maricopa has the most aggressive bulk-data publishing of the five** — the Assessor's "Data Downloads" page exposes 12+ ready-made datasets (sales, ownership, valuation, residential characteristics).

| Category | Name | URL | Format | Auth | Cost | Refresh | Sample data | Verified |
|---|---|---|---|---|---|---|---|---|
| Assessor portal | MCAssessor home | https://mcassessor.maricopa.gov/ | HTML form | none | free | daily | parcel/owner search | 200 |
| Assessor portal | MCAssessor mcs.php (parcel search) | https://mcassessor.maricopa.gov/mcs.php | HTML form | none | free | daily | typed query interface | 200 |
| Assessor REST | gis.mcassessor root services directory | https://gis.mcassessor.maricopa.gov/arcgis/rest/services | ArcGIS REST | none | free | daily | full Assessor services tree | 200 |
| Assessor REST | Parcels MapServer | https://gis.mcassessor.maricopa.gov/arcgis/rest/services/Parcels/MapServer | ArcGIS REST | none | free | daily | parcel polygons | 200 |
| Assessor REST | Parcels Layer 0 | https://gis.mcassessor.maricopa.gov/arcgis/rest/services/Parcels/MapServer/0 | ArcGIS REST | none | free | daily | queryable parcel attributes | 200 |
| Assessor REST | MaricopaDynamicQueryService | https://gis.mcassessor.maricopa.gov/arcgis/rest/services/MaricopaDynamicQueryService/MapServer | ArcGIS REST | none | free | daily | parcels + subdivisions + sections + MapIDs | 200 |
| Assessor REST | MapIDs MapServer | https://gis.mcassessor.maricopa.gov/arcgis/rest/services/MapIDs/MapServer | ArcGIS REST | none | free | daily | book-map-parcel grid | 200 |
| Assessor bulk | Data Requests page (bulk file index) | https://www.maricopa.gov/509/Data-Requests | HTML form | none | free | varies | Assessor + GIS bulk download menu | 200 |
| Assessor bulk | GIS Mapping Applications hub | https://www.maricopa.gov/3942/GIS-Mapping-Applications | HTML form | none | free | varies | links to viewers + downloads | 200 |
| Assessor bulk | GIS Maps program page | https://www.maricopa.gov/507/GIS-Maps | HTML form | none | free | varies | spatial dept landing | 200 |
| Assessor bulk | Interactive Parcel Maps | https://www.maricopa.gov/4035/Interactive-Parcel-Maps | HTML form | none | free | varies | ortho + parcel viewers | 200 |
| County GIS | Maricopa Open Data (ArcGIS Hub) | https://data-maricopa.opendata.arcgis.com/ | ArcGIS Hub | none | free | daily-weekly-monthly | parcels, ortho, addresses | 200 |
| County GIS | Parcels dataset on Hub | https://data-maricopa.opendata.arcgis.com/datasets/c937f17330f64e64abd41976fc8bb17f | ArcGIS Hub | none | free | weekly | full county parcel SHP/GeoJSON | 200 |
| County GIS | Maricopa AGO services (Hg9YFFiMnUg5h3yU) | https://services2.arcgis.com/Hg9YFFiMnUg5h3yU/arcgis/rest/services | ArcGIS REST | none | free | rolling | county hosted services org | 200 |
| County GIS | Parcels FeatureServer (Hg9YFFiMnUg5h3yU) | https://services2.arcgis.com/Hg9YFFiMnUg5h3yU/arcgis/rest/services/Parcels/FeatureServer | ArcGIS REST | none | free | weekly | parcel FS layer | 200 |
| County GIS | Parcels FeatureServer Layer 0 | https://services2.arcgis.com/Hg9YFFiMnUg5h3yU/arcgis/rest/services/Parcels/FeatureServer/0 | ArcGIS REST | none | free | weekly | queryable parcels | 200 |
| County GIS | Maricopa Parcels FS (alternate) | https://services2.arcgis.com/Hg9YFFiMnUg5h3yU/arcgis/rest/services/MaricopaParcels/FeatureServer | ArcGIS REST | none | free | weekly | named-alias parcel layer | 200 |
| County GIS | Address Points FS | https://services2.arcgis.com/Hg9YFFiMnUg5h3yU/arcgis/rest/services/AddressPoints/FeatureServer | ArcGIS REST | none | free | weekly | site-address point layer | 200 |
| Recorder | Recorder office home | https://recorder.maricopa.gov/ | HTML form | none | free | daily | office landing (HEAD blocked) | 403 (browser-reachable) |
| Recorder | Recorded Document Search (current) | https://recorder.maricopa.gov/recording/document-search.html | HTML form | none | free | daily | name/date deed search | 403 (browser-reachable) |
| Recorder | Legacy Recorded Document Search | https://legacy.recorder.maricopa.gov/recdocdata/ | HTML form | none | free | daily | full historical index back further than 2y | 200 |
| Treasurer | Treasurer home | https://treasurer.maricopa.gov/ | HTML form | none | free | daily | tax-bill viewer | 200 |
| Treasurer | Parcel inquiry | https://treasurer.maricopa.gov/Parcel | HTML form | none | free | daily | per-parcel taxes paid/owed | 200 |
| Tax-lien sale | Tax Lien Sale (RealAuction) | https://maricopa.arizonataxsale.com | HTML form | account | free | annual (Feb) | online tax-lien certificate auction | 403 (browser-reachable) |
| Tax-lien sale | Lien & Delinquent Parcels app (ArcGIS Experience) | https://experience.arcgis.com/experience/bd50c51b89054238bfadf69e91b421c9 | ArcGIS REST | none | free | annual | spatial map of CP-eligible parcels | 200 |
| Tax-deed sale | Tax Deeded Land Sales | https://www.maricopa.gov/780/Tax-Deeded-Land-Sales | HTML form | none | free | annual | Board-of-Supervisors tax-deed auction | 200 |
| Civil/foreclosure court | Superior Court | https://superiorcourt.maricopa.gov/ | HTML form | none | free | daily | court landing | 200 |
| Civil/foreclosure court | Civil case docket | https://www.superiorcourt.maricopa.gov/docket/CivilCourtCases/ | HTML form | none | free | daily | judicial-foreclosure case docket | 200 |
| Eviction court | Justice Courts home | https://justicecourts.maricopa.gov/ | HTML form | none | free | daily | landing | 200 |
| Eviction court | Justice Court case search | https://justicecourts.maricopa.gov/app/courtrecords/casesearch | HTML form | none | free | daily | small-claims + FED evictions | 200 |
| Eviction court | Eviction case-type page | https://justicecourts.maricopa.gov/case-types/evictions | HTML form | none | free | daily | FED procedure | 200 |
| Eviction court | AZ Courts eviction-stats dashboard | https://www.azcourts.gov/statistics/Interactive-Data-Dashboards/Justice-Court-Evictions | HTML form | none | free | monthly | statewide eviction-filings counts | 200 |

---

## Cross-county notes

- The five counties together publish **150+ verified free endpoints**.
- **Tax-sale platforms** are split: Cuyahoga + Maricopa run on RealAuction; Cook runs on a RealAuction-branded `cooktaxsale.com`; Philadelphia runs on Bid4Assets; Harris is the only one that is still in-person (Constable auction at the Bayou City Event Center).
- **Probate online access**: only Cuyahoga and Cook expose a true searchable probate docket. Harris has it via the County Clerk's `Probate.aspx`. Maricopa's Superior Court probate search is inside the Superior Court civil-docket app. Philadelphia's Register of Wills has **no online docket**.
- For ArcGIS REST endpoints, every county exposes parcel polygons via at least one Esri endpoint — the most useful are Harris (`gis.hctx.net/.../HCAD/Parcels`), Maricopa (`gis.mcassessor.maricopa.gov/.../Parcels`), Cook (`services3.arcgis.com/U35BQTmVA2MAFFlt/.../Parcel`), and Philadelphia (`services.arcgis.com/fLeGjb7u4uXqeF9q/.../OPA_Properties_Public`).
- Cuyahoga's GIS server (`gis.cuyahogacounty.us/server/rest/services`) returns 403 to public clients but the data is mirrored via the ArcGIS Hub portals listed above.
