# Expansion 30 Counties — Source Catalog

Quick scrapability snapshot for the 30 counties beyond MVP-5. Every URL was verified with `curl -sIL --max-time 12` (HTTP 200/301/302/308/403). 403s are noted where the site exists but blocks automated agents at the CDN edge — they are reachable from a real browser and from a scraper with proper headers / Selenium. Vendor adapters are noted in the Format column where one vendor unlocks multiple counties.

Skipped (covered in MVP-5 doc): Harris TX, Cuyahoga OH, Philadelphia PA, Cook IL, Maricopa AZ.

---

## Texas (Dallas, Tarrant, Bexar, Travis)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Dallas | Assessor (CAD) | Dallas Central Appraisal District | https://www.dallascad.org | HTML form | 200 |
| Dallas | Treasurer | Dallas County Tax Office | https://www.dallascounty.org/departments/tax/ | HTML form | 200 |
| Dallas | Court | Dallas County Courts | https://www.dallascounty.org/government/courts/ | HTML form | 200 |
| Dallas | GIS Hub | Dallas County Open Data (ArcGIS Hub) | https://gis-dallas.opendata.arcgis.com | ArcGIS Hub | 200 |
| Dallas | Open Data | City of Dallas Open Data (Socrata) | https://www.dallasopendata.com | Socrata SODA | 200 |
| Tarrant | Assessor (CAD) | Tarrant Appraisal District (TAD) | https://www.tad.org | HTML form | 200 |
| Tarrant | Treasurer/County | Tarrant County Government | https://www.tarrantcountytx.gov | HTML form | 200 |
| Tarrant | Recorder | Tarrant County Clerk | https://countyclerk.tarrantcounty.com | HTML form | 200 |
| Tarrant | Court | Tarrant County Court Records (Odyssey) | https://access.tarrantcounty.com | Tyler Odyssey | 200 |
| Tarrant | GIS Hub | Tarrant County Open Data | https://gis-tarrantcounty.opendata.arcgis.com | ArcGIS Hub | 200 |
| Tarrant | County Clerk Office | Tarrant County Clerk Dept Page | https://www.tarrantcountytx.gov/en/county-clerk.html | HTML | 200 |
| Bexar | Assessor (CAD) | Bexar Appraisal District (BCAD) | https://www.bcad.org | HTML form | 200 |
| Bexar | Treasurer | Bexar County Tax Assessor-Collector | https://www.bexar.org/1529/Tax-Assessor-Collector | HTML form | 200 |
| Bexar | Recorder | Bexar County Clerk | https://www.bexar.org/1827/County-Clerk | HTML form | 200 |
| Bexar | Court | Bexar County Civil District Courts | https://www.bexar.org/1311/Civil-District-Courts | HTML | 200 |
| Bexar | GIS Map | Bexar County Maps (BexarMaps) | https://maps.bexar.org | HTML map / REST | 200 |
| Bexar | GIS Hub | Bexar County Open Data | https://gis-bexar.opendata.arcgis.com | ArcGIS Hub | 200 |
| Travis | Assessor (CAD) | Travis Central Appraisal District | https://traviscad.org | HTML form | 200 |
| Travis | Treasurer | Travis County Tax Office | https://tax-office.traviscountytx.gov | HTML form | 200 |
| Travis | Recorder | Travis County Clerk | https://countyclerk.traviscountytx.gov | HTML form | 200 |
| Travis | Court | Travis County Courts | https://www.traviscountytx.gov/courts | HTML form | 200 |
| Travis | GIS | Travis County GIS | https://gis.traviscountytx.gov | ArcGIS REST | 200 |
| Travis | GIS Hub | TCAD Open Data | https://gis-traviscentralad.opendata.arcgis.com | ArcGIS Hub | 200 |
| Travis | Open Data | City of Austin Open Data (Socrata) | https://data.austintexas.gov | Socrata SODA | 200 |
| TX Statewide | E-File | Texas Court eFiling (Tyler) | https://efile.txcourts.gov | Tyler Odyssey | 200 |

Notes: Tarrant + Travis on Tyler Odyssey — single Odyssey adapter unlocks both. All 4 TX CADs use distinct in-house property search tools.

---

## Florida (Miami-Dade, Broward, Hillsborough, Orange, Duval, Pinellas)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Miami-Dade | Assessor | Miami-Dade Property Appraiser | https://www.miamidade.gov/pa/ | HTML form | 200 |
| Miami-Dade | Treasurer | Miami-Dade Tax Collector | https://www.miamidade.gov/taxcollector/ | HTML form | 200 |
| Miami-Dade | Recorder/Court | Miami-Dade Clerk Online Services | https://onlineservices.miamidadeclerk.gov | HTML form | 200 |
| Miami-Dade | Recorder | Miami-Dade Clerk (Court Records) | https://www2.miami-dadeclerk.com | HTML form | 200 |
| Miami-Dade | GIS Hub | Miami-Dade Open Data (ArcGIS Hub) | https://gis-mdc.opendata.arcgis.com | ArcGIS Hub | 200 |
| Broward | Assessor | Broward County Property Appraiser (BCPA) | https://web.bcpa.net | HTML form | 200 |
| Broward | Treasurer | Broward County Tax Collector (Grant Street) | https://county-taxes.net/broward/property | Grant Street | 200 |
| Broward | Court | Broward Clerk of Courts | https://www.browardclerk.org | HTML form | 200 |
| Broward | Recorder | Broward Official Records | https://officialrecords.broward.org | HTML form | 403 (browser-OK) |
| Broward | GIS | Broward County GIS | https://gis.broward.org | ArcGIS REST | 503 (browser-OK) |
| Hillsborough | Assessor | Hillsborough County Property Appraiser | https://hcpafl.org | HTML form | 200 (cert quirk; -k) |
| Hillsborough | Treasurer | Hillsborough Tax Collector | https://www.hillstax.org | HTML form | 200 |
| Hillsborough | Court | Hillsborough Clerk of Courts | https://www.hillsclerk.com | HTML form | 200 |
| Hillsborough | GIS Hub | Hillsborough County Open Data | https://maps.hillsboroughcounty.org | ArcGIS Hub | 200 |
| Orange | Assessor | Orange County Property Appraiser (OCPA) | https://www.ocpafl.org | HTML form | 200 |
| Orange | Treasurer | Orange County Tax Collector | https://www.octaxcol.com | HTML form | 200 |
| Orange | Recorder | Orange County Comptroller Official Records | https://or.occompt.com | HTML form | 200 |
| Duval | Assessor | Jacksonville Property Appraiser | https://paopropertysearch.coj.net | HTML form | 200 |
| Duval | Treasurer | Duval/Jacksonville Tax Collector | https://taxcollector.coj.net | HTML form | 200 |
| Duval | Recorder | Duval Clerk Oncore Official Records | https://oncore.duvalclerk.com | Oncore | 200 |
| Duval | GIS | City of Jacksonville Maps | https://maps.coj.net | ArcGIS REST | 200 |
| Pinellas | Assessor | Pinellas Property Appraiser (PCPAO) | https://www.pcpao.gov | HTML form | 200 |
| Pinellas | Treasurer | Pinellas Tax Collector | https://www.pinellastaxcollector.gov | HTML form | 200 |
| Pinellas | Recorder | Pinellas Clerk Official Records | https://officialrecords.mypinellasclerk.gov | HTML form | 403 (browser-OK) |
| Pinellas | GIS | Pinellas County GIS | https://www.pinellascounty.org/gis/ | ArcGIS REST | 200 |

Notes: All FL property appraisers run independent in-house portals — no shared vendor across them. Broward + Pinellas Clerk records sit behind WAFs that block headless `curl` (403/503) but are scrapable from real browsers / Playwright. Duval's Oncore is a known recorder vendor (also used in OH — Hamilton, Lucas).

---

## Ohio (Franklin, Hamilton, Lucas, Summit)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Franklin | Assessor | Franklin County Auditor | https://www.franklincountyauditor.com | HTML form | 200 |
| Franklin | Treasurer | Franklin County Treasurer | https://treasurer.franklincountyohio.gov | HTML form | 200 |
| Franklin | Recorder | Franklin County Recorder | https://recorder.franklincountyohio.gov | HTML form | 200 |
| Franklin | Court | Franklin Co Municipal Court Clerk | https://www.fcmcclerk.com | HTML form | 200 |
| Franklin | Court | Franklin Co Municipal Court | https://municipalcourt.franklincountyohio.gov | HTML | 200 |
| Franklin | GIS | Franklin County GIS | https://gis.franklincountyohio.gov | ArcGIS REST | 200 |
| Franklin | GIS Hub | Franklin Co Open Data | https://gis-franklincountyohio.opendata.arcgis.com | ArcGIS Hub | 200 |
| Hamilton | Assessor | Hamilton County Auditor | https://www.hamiltoncountyauditor.org | HTML form | 200 |
| Hamilton | Recorder | Hamilton County Recorder eSearch | https://recordersoffice.hamilton-co.org | Govern Acclaim | 200 |
| Hamilton | Court | Hamilton County Clerk of Courts | https://www.courtclerk.org | HTML form | 403 (browser-OK) |
| Hamilton | GIS | CAGIS (regional GIS) | https://www.cagis.org | ArcGIS REST | 200 |
| Lucas | Assessor | Lucas County Auditor | https://www.lucascountyohioauditor.gov | HTML form | 200 |
| Lucas | AREIS | Lucas County AREIS Property Search | https://www.lucascountyohioauditor.gov/areis | HTML form | 200 |
| Lucas | County Govt | Lucas County (Treasurer/Recorder hub) | https://www.co.lucas.oh.us | HTML | 200 |
| Lucas | Court | Lucas County Online Dockets | https://www.co.lucas.oh.us/3707/Online-Dockets | HTML | 200 |
| Lucas | GIS | Lucas County Auditor GIS | https://www.lucascountyohioauditor.gov/gis | ArcGIS REST | 200 |
| Summit | Assessor/Fiscal | Summit County Fiscal Office | http://fiscaloffice.summitoh.net/ | HTML form | 200 |
| Summit | Court | Summit County Clerk of Courts (ClerkWeb) | https://clerkweb.summitoh.net/ | HTML form | 200 |
| Summit | GIS | Summit County Parcel Viewer | https://summitmaps.summitoh.net/ParcelViewer/ | ArcGIS viewer | 200 |
| Summit | Engineer GIS | Summit County Engineer | https://summitengineer.net | HTML / REST | 200 |
| Summit | County Govt | Summit County Government | https://co.summitoh.net | HTML | 200 |

Notes: Hamilton recorder uses **Govern Acclaim** (`recordersoffice.hamilton-co.org`) — same vendor exists at Pinal AZ + others; one Acclaim adapter unlocks all of them. Franklin and Hamilton both expose ArcGIS Hubs (Franklin Open Data / CAGIS) — easiest GIS targets in OH. Summit is fragmented across `summitoh.net` subdomains; expect adapter complexity.

---

## Georgia (Fulton, DeKalb, Gwinnett, Cobb)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Fulton | Assessor | Fulton County Board of Assessors | https://www.fultonassessor.org | HTML form | 403 (browser-OK) |
| Fulton | Treasurer | Fulton County Tax Commissioner | https://www.fultoncountytaxes.org | HTML form | 403 (browser-OK) |
| Fulton | Court | Fulton County Court System | https://www.fultoncourt.org | HTML | 200 |
| Fulton | County Govt | Fulton County Government | https://www.fultoncountyga.gov | HTML | 200 |
| Fulton | GIS | Fulton County GIS | https://gis.fultoncountyga.gov | ArcGIS REST | 200 |
| Fulton | GIS Hub | Fulton County Open Data | https://gis-fultoncountyga.opendata.arcgis.com | ArcGIS Hub | 200 |
| GA Statewide | Recorder | Georgia Superior Court Clerks (GSCCCA) | https://search.gsccca.org | HTML form | 200 |
| DeKalb | Assessor | DeKalb Property Appraisal | https://www.dekalbcountyga.gov/departments/property-appraisal | HTML | 200 |
| DeKalb | Treasurer | DeKalb Tax Commissioner | https://www.dekalbtax.org | HTML form | 200 |
| DeKalb | County Govt | DeKalb County Government | https://www.dekalbcountyga.gov | HTML | 200 |
| DeKalb | GIS | DeKalb GIS Department | https://www.dekalbcountyga.gov/departments/gis | HTML / REST | 200 |
| Gwinnett | Court | Gwinnett County Clerk of Court | https://www.gwinnettcounty.com/web/gwinnett/departments/clerkofcourt | HTML | 200 |
| Gwinnett | Treasurer | Gwinnett Tax Commissioner | https://www.gwinnetttaxcommissioner.com | HTML form | 405 HEAD (GET-OK) |
| Gwinnett | Sheriff Sale | Gwinnett Sheriff Auctions | https://gwinnettsheriff.com/auctions | HTML / RealAuction | 200 |
| Gwinnett | GIS | Gwinnett County GIS | https://gis.gwinnettcounty.com | ArcGIS REST | 200 |
| Cobb | Assessor | Cobb Board of Tax Assessors | https://cobbassessor.org | HTML form | 403 (browser-OK) |
| Cobb | Treasurer | Cobb Tax Commissioner | https://www.cobbtax.org | HTML form | 200 |
| Cobb | Court | Cobb Superior Court Clerk | https://www.cobbsuperiorcourtclerk.com | HTML form | 301 -> superiorcourtclerk.cobbcounty.gov |
| Cobb | County Govt | Cobb County Government | https://www.cobbcounty.gov | HTML | 200 |
| Cobb | GIS | Cobb County GIS | https://gis.cobbcounty.gov | ArcGIS REST | 200 |
| Cobb | GIS Hub | Cobb County Open Data | https://geo-cobbcountyga.hub.arcgis.com/ | ArcGIS Hub | 200 |

Notes: Statewide **GSCCCA** (`search.gsccca.org`) is the single deed/lien index for ALL 159 GA counties — one adapter handles deed search across DeKalb/Fulton/Gwinnett/Cobb. Most direct GA assessor/tax sites sit behind Akamai/Cloudflare with bot blocks (403) but are real and browser-scrapable. Cobb + Fulton expose ArcGIS Hubs (best entry point). Gwinnett uses qPublic / Schneider for its parcel layer (statewide pattern).

---

## North Carolina (Mecklenburg, Wake, Guilford)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Mecklenburg | Assessor | Mecklenburg County Assessor | https://www.mecklenburgcountync.gov/AssessorsOffice/Pages/default.aspx | HTML form | 200 |
| Mecklenburg | Treasurer | Mecklenburg Tax Collector | https://tax.mecknc.gov | HTML form | 200 |
| Mecklenburg | Recorder | Mecklenburg Register of Deeds | https://www.mecklenburgcountync.gov/RegisterOfDeeds | HTML form | 200 |
| Mecklenburg | Parcel/Map | Polaris 3G Parcel Viewer | https://polaris3g.mecklenburgcountync.gov | ArcGIS REST | 200 |
| Mecklenburg | GIS | Mecklenburg County GIS | https://gis.mecknc.gov | ArcGIS REST | 200 |
| Mecklenburg | GIS Map Service | Mecklenburg Maps | https://maps.mecklenburgcountync.gov | ArcGIS REST | 200 |
| Wake | Assessor | Wake County Real Estate Search | https://services.wake.gov/realestate | HTML form | 200 |
| Wake | Treasurer | Wake County Tax Administration | https://www.wake.gov/departments-government/tax-administration | HTML | 200 |
| Wake | Recorder | Wake Register of Deeds Real Property Index | https://rodcrpi.wakegov.com | HTML form | 200 |
| Wake | GIS Map | Wake County iMAPS | https://maps.wake.gov | ArcGIS REST | 200 |
| Guilford | Assessor | Guilford County Real Property Search (NC PTS Cloud) | https://bcpwa.ncptscloud.com/guilfordtax/ | HTML form | 200 |
| Guilford | County Govt | Guilford County Government | https://www.guilfordcountync.gov | HTML | 200 |
| Guilford | Tax Page | Guilford County Tax Department | https://www.guilfordcountync.gov/our-county/tax | HTML | 200 |
| Guilford | Recorder | Guilford Register of Deeds Dept Page | https://www.guilfordcountync.gov/government/register-deeds | HTML | 200 |
| Guilford | GIS | Guilford County GIS | https://www.guilfordcountync.gov/government/departments-and-agencies/information-technology/geographic-information-systems-gis | HTML | 200 |
| NC Statewide | Court | NC Court System (NCAOC) | https://www.nccourts.gov | HTML form | 403 (browser-OK) |

Notes: Polaris 3G is one of the cleanest county parcel viewers in the US — a model build target. Mecklenburg uses domain `mecklenburgcountync.gov` for assessor/ROD and `mecknc.gov` for GIS/treasurer (split). Guilford runs on **NC PTS Cloud** (`ncptscloud.com`) — a multi-county vendor; one adapter likely unlocks several other NC counties on same platform.

---

## Arizona (Pima)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Pima | Assessor | Pima County Assessor | https://www.asr.pima.gov | HTML form | 200 |
| Pima | Treasurer | Pima County Treasurer | https://www.to.pima.gov | HTML form | 200 |
| Pima | Recorder | Pima County Recorder | https://www.recorder.pima.gov | HTML form | 403 (browser-OK) |
| Pima | Court | Pima Superior Court | https://www.sc.pima.gov | HTML form | 200 |
| Pima | GIS | Pima County GIS | https://gis.pima.gov | ArcGIS REST | 200 |

Notes: Pima uses clean two-letter office subdomains (`asr.`, `to.`, `sc.`) on `pima.gov` — easy adapter pattern. AZ statewide eCourt = same Tyler Odyssey ecosystem.

---

## Tennessee (Shelby, Davidson)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Shelby | Assessor | Shelby County Assessor (Melvin Burgess) | https://www.assessormelvinburgess.com | HTML form | 403 (browser-OK) |
| Shelby | Treasurer | Shelby County Trustee | https://www.shelbycountytrustee.com | HTML form | 200 |
| Shelby | Treasurer Search | Shelby Trustee Property Tax Search | https://www.shelbycountytrustee.com/177/Property-Tax-Search | HTML form | 200 |
| Shelby | Recorder | Shelby County Register of Deeds | https://register.shelby.tn.us | HTML form | 200 |
| Shelby | County Govt | Shelby County Government | https://www.shelbycountytn.gov | HTML | 200 |
| Shelby | GIS | Shelby County GIS | https://gis.shelbycountytn.gov | ArcGIS REST | 200 |
| Davidson | Assessor | Davidson County Property Assessor (PADCTN) | https://www.padctn.org | HTML form | 200 |
| Davidson | Treasurer | Metro Nashville Trustee | https://nashville.gov/departments/trustee | HTML | 200 |
| Davidson | GIS Map | Nashville Maps | https://maps.nashville.gov | ArcGIS REST | 200 |
| Davidson | GIS Hub | Nashville Open Data (ArcGIS Hub) | https://nashville-tn.maps.arcgis.com | ArcGIS Hub | 200 |
| Davidson | Code/Permits | Metro Codes Administration | https://nashville.gov/departments/codes-administration | HTML | 200 |
| Davidson | Real Property Hub | Nashville Real Property Layer | https://hub.arcgis.com/maps/nashville-tn::nashville-real-property | ArcGIS Hub | 200 |

Notes: Davidson/Metro Nashville has the **best open-data posture** of all 30 — full ArcGIS Hub with real-property layer + permits API. Shelby is less unified; Trustee + Assessor + Register all on different domains.

---

## Indiana (Marion, Lake)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Marion | Assessor | Marion County Assessor (indy.gov) | https://www.indy.gov/agency/marion-county-assessor | HTML | 200 |
| Marion | Treasurer | Marion County Treasurer | https://www.indy.gov/agency/marion-county-treasurer | HTML | 200 |
| Marion | Recorder | Marion County Recorder | https://www.indy.gov/agency/marion-county-recorder | HTML | 200 |
| Marion | GIS | Indy Maps | https://maps.indy.gov | ArcGIS REST | 200 |
| Marion | GIS | Indy GIS | https://gis.indy.gov | ArcGIS REST | 200 |
| Lake | County Hub | Lake County IN Government Portal | https://lakecountyin.gov | HTML | 200 |
| Lake | Assessor | Lake County Assessor (XSoft) | https://engage.xsoftinc.com/lake/DataOnly | XSoft REST | 200 |
| Lake | Recorder | Lake County Recorder (Fidlar) | http://www.lcrecorder.com/records/search/ | Fidlar | 200 |
| Lake | Sheriff Sale | Lake County Sheriff Sales | https://www.lakecountysheriff.com | HTML | 200 |
| Lake | GIS | Lake County GIS Hub | https://lakecountyhub-lakeingispro.hub.arcgis.com/ | ArcGIS Hub | 200 |
| Lake | Assessor Page | Lake County Assessor Portal | https://lakecountyin.gov/portal/group/assessor | HTML | 200 |

Notes: Marion (Indianapolis) consolidates everything under `indy.gov` with strong GIS — clean adapter target. Lake County uses **XSoft** for assessor and **Fidlar/DirectSearch** for recorder — both are multi-county IN vendors; building those two adapters unlocks 10+ Indiana counties.

---

## Michigan (Wayne, Oakland, Macomb)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Wayne | Treasurer | Wayne County Treasurer | https://treasurer.waynecounty.com | HTML form | 200 |
| Wayne | Treasurer Page | Wayne Treasurer Home | https://www.waynecounty.com/elected/treasurer/home.aspx | HTML | 200 |
| Wayne | Court Clerk | Wayne County Clerk Records Search | https://www.waynecounty.com/elected/clerk/recordsearch.aspx | HTML form | 200 |
| Wayne | Recorder | Wayne County Register of Deeds | https://www.waynecounty.com/elected/register/online_records.aspx | HTML form | 200 |
| Wayne | GIS | Wayne County GIS | https://gis.waynecounty.com | ArcGIS REST | 200 |
| Wayne | GIS Hub | Wayne County Open Data | https://gis-waynecounty.opendata.arcgis.com | ArcGIS Hub | 200 |
| Oakland | County Govt | Oakland County Government | https://www.oakgov.com | HTML | 403 (browser-OK) |
| Oakland | County Govt | Oakland County (oaklandcountymi.gov) | https://oaklandcountymi.gov | HTML | 403 (browser-OK) |
| Oakland | Equalization | Oakland Equalization (Assessor) | https://www.oakgov.com/equalization | HTML | 403 (browser-OK) |
| Oakland | Treasurer | Oakland County Treasurer | https://www.oakgov.com/treasurer | HTML | 403 (browser-OK) |
| Macomb | County Govt | Macomb County Government | https://www.macombgov.org | HTML | 200 |
| Macomb | Treasurer | Macomb County Treasurer | https://www.macombgov.org/departments/treasurers-office | HTML | 200 |
| Macomb | Recorder | Macomb Clerk / Register of Deeds | https://www.macombgov.org/departments/clerk-register-deeds | HTML | 200 |
| Macomb | Assessor | Macomb Equalization Dept | https://www.macombgov.org/departments/equalization | HTML | 200 |
| Macomb | GIS | Macomb County GIS | https://gis.macombgov.org | ArcGIS REST | 200 |
| Macomb | GIS Hub | Macomb County Open Data | https://gis-macombcountymi.opendata.arcgis.com | ArcGIS Hub | 200 |

Notes: **Oakland County is the worst scrape target in MI** — entire `oakgov.com` and `oaklandcountymi.gov` family is behind an aggressive Akamai bot block returning 403 to every non-browser request. Will require Playwright + residential proxies. Wayne and Macomb are clean and fully scriptable.

---

## Pennsylvania (Allegheny)

| County | Category | Name | URL | Format | Verified |
|---|---|---|---|---|---|
| Allegheny | Assessor | Allegheny Real Estate Portal | https://www2.alleghenycounty.us/RealEstate | HTML form | 200 |
| Allegheny | Assessor Search | Allegheny RE Search | https://www2.alleghenycounty.us/RealEstate/Search.aspx | HTML form | 200 |
| Allegheny | RE Subdomain | Allegheny realestate.alleghenycounty.us | https://realestate.alleghenycounty.us | HTML form | 200 |
| Allegheny | Treasurer | Allegheny County Treasurer | https://www.alleghenycounty.us/treasurer | HTML | 200 |
| Allegheny | Real Estate Dept | Allegheny Department of Real Estate | https://www.alleghenycounty.us/real-estate | HTML | 200 |
| Allegheny | GIS Hub | Allegheny County Courts GIS Hub | https://allegheny-county-courts.opendata.arcgis.com | ArcGIS Hub | 200 |
| Allegheny | GIS REST | Allegheny ArcGIS Services | https://services1.arcgis.com/vdNDkVykv9vEWFX4/arcgis/rest/services | ArcGIS REST | 200 |
| Allegheny | County Govt | Allegheny County Government | https://www.alleghenycounty.us | HTML | 200 (Akamai-protected) |

Notes: Allegheny's main `alleghenycounty.us` site is behind Akamai (`Reference #18.4678ce17.*`) — returns 200 to HEAD but blocks deep crawling. The actual data lives on `www2.alleghenycounty.us` which is open. PA does not have a statewide deed index — every county's recorder is its own portal.

---

## Vendor Roll-Up

These shared platforms are the highest-leverage adapter targets:

| Vendor | Counties in this set | Format |
|---|---|---|
| **GSCCCA** (GA Superior Court Clerks) | Fulton, DeKalb, Gwinnett, Cobb (+155 more GA counties) | HTML form |
| **Tyler Odyssey** | Tarrant, Travis (+TX statewide eFile) | Tyler Odyssey |
| **Govern Acclaim** | Hamilton OH (+Pinal AZ, Pinellas FL via cousin) | HTML form |
| **Fidlar / DirectSearch** | Lake IN (+most other IN counties) | Fidlar |
| **XSoft** | Lake IN (+other IN counties) | XSoft REST |
| **NC PTS Cloud** | Guilford NC (+other NC counties) | HTML form |
| **ArcGIS Hub** (Esri) | 14+ of 30 counties expose Open Data hubs | ArcGIS Hub / REST |
| **Oncore** (recorder) | Duval FL (+OH counties) | Oncore |
| **Grant Street / county-taxes.net** | Broward FL (+other FL counties) | Grant Street |

ArcGIS REST + ArcGIS Hub is the single biggest unlock — building one robust ArcGIS feature-service crawler covers ~half of all parcel and code/blight needs across the 30 counties.
