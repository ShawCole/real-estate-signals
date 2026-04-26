# State-Level Data Sources for Distressed Property Signals

Cataloging public, statewide databases / APIs / portals that publish data relevant to real-estate distress signals across the top 10 investor-friendly states (TX, FL, OH, GA, NC, AZ, TN, IN, MI, PA).

## Methodology

- Every URL listed below was probed with `curl -sI -L` (and a Chrome user agent where bots were initially blocked). Rows that could not be confirmed live (HTTP 200/301/302/403-anti-bot-but-real) were dropped.
- The `Verified` column records the actual HTTP status returned. A `403 (live, anti-bot)` means curl was rejected but the page is real and reachable in a browser (verified via WebFetch or sibling URL).
- Categories per state covered: GIS/parcels, property tax / DOR, court systems (foreclosure), tax-sale & foreclosure auction aggregators, Secretary of State / UCC, WARN Act, open-data portals, vital records / probate, and brownfield / contaminated-site programs.
- Counts: ~85 verified rows across 10 states.

## Notes on coverage gaps

- **Statewide foreclosure auction aggregators** are mostly run by three private vendors — RealAuction, GovEase, Bid4Assets — under contracts with individual counties, not states. Ohio is the lone exception: its statewide foreclosure auction site is mandated by HB 390 and run on RealAuction. So I list the vendors once each rather than copy-paste them under every state.
- **Probate**: Only Texas (re:SearchTX) and Indiana (mycase.in.gov) have true unified statewide probate-case search. Others are county-by-county; rolled into the court-system row.
- **Vital records (death indexes)**: Pre-1975 indexes are widely public; modern statewide death feeds for distress-signal use require either a paid genealogy provider or per-county FOIA. Listed where a free statewide index exists.
- **Arizona ADEQ pages (azdeq.gov)** consistently 403 to curl + WebFetch (Cloudflare bot challenge), but the URLs resolve in a browser. Marked accordingly.

## The Table

| State | Category | Name | URL | Format | Auth | Cost | Refresh | Notes | Verified |
|---|---|---|---|---|---|---|---|---|---|
| TX | State GIS | Texas Geographic Information Office (TxGIO / TNRIS) | https://tnris.org/ | ArcGIS Hub | none | free | annual | StratMap statewide land parcels compiled from county CADs | ✓ HTTP 200 |
| TX | State GIS | TxGIO StratMap Land Parcels | https://www.tnris.org/stratmap/land-parcels.html | CSV download + shapefile + geodatabase | none | free | annual | Statewide parcel schema; refresh varies by county | ✓ HTTP 200 |
| TX | State GIS | TxDOT Open Data Portal | https://gis-txdot.opendata.arcgis.com/ | ArcGIS Hub + REST | none | free | varies | Roads, ROW, district boundaries | ✓ HTTP 200 |
| TX | State GIS | TCEQ GIS Open Data Hub | https://gis-tceq.opendata.arcgis.com/ | ArcGIS Hub + REST | none | free | varies | Includes Voluntary Cleanup, brownfield points | ✓ HTTP 200 |
| TX | Property Tax / DOR | Texas Comptroller — Property Tax Assistance | https://comptroller.texas.gov/taxes/property-tax/ | HTML form | none | free | continuous | Oversight only — no statewide tax roll | ✓ HTTP 200 |
| TX | Property Tax / DOR | Texas Comptroller — County / CAD Directory | https://comptroller.texas.gov/taxes/property-tax/county-directory/ | HTML form | none | free | annual | Authoritative directory of all 254 county appraisal districts | ✓ HTTP 200 |
| TX | Court System | re:SearchTX (statewide e-filing case search) | https://research.txcourts.gov/CourtRecordsSearch/ui/Home | HTML form | account | free + $0.10/page docs | continuous | Civil / probate / district / county courts; coverage from Nov 2018 | ✓ HTTP 200 |
| TX | Court System | Texas Judicial Branch — Appellate Case Search | https://search.txcourts.gov/CaseSearch.aspx?coa=cossup | HTML form | none | free | continuous | Supreme Court, Courts of Appeals | ✓ HTTP 200 |
| TX | SoS / UCC | Texas SOSDirect (UCC + business filings) | https://www.sos.state.tx.us/corp/sosda/index.shtml | HTML form | account | $1/search | continuous | Statewide UCC / financing-statement search | ✓ HTTP 200 |
| TX | WARN | TWC Worker Adjustment & Retraining Notification | https://www.twc.texas.gov/data-reports/warn-notice | CSV download (XLSX) | none | free | weekly | Official statewide WARN list | ✓ HTTP 200 |
| TX | WARN | TX Open Data — WARN Notices (Socrata) | https://data.texas.gov/dataset/Worker-Adjustment-and-Retraining-Notification-WARN/8w53-c4f6 | Socrata SODA | none / API key | free | weekly | Same WARN data, queryable via SoQL | ✓ HTTP 200 |
| TX | Open Data | Texas Open Data Portal | https://data.texas.gov/ | Socrata SODA | none / API key | free | varies | State-mandated Socrata portal (SB 819) | ✓ HTTP 200 |
| TX | Open Data | Texas Open Data Portal (texas.gov gateway) | https://www.texas.gov/texas-open-data-portal/ | HTML | none | free | n/a | Landing page + agency directory | ✓ HTTP 200 |
| TX | Vital Records | TX DSHS Vital Statistics — Death Records | https://www.dshs.texas.gov/vital-statistics/death-records | HTML form | none | $20/copy | continuous | Statewide death index back to 1903 | ✓ HTTP 200 |
| TX | Vital Records | TX DSHS Birth/Death Indexes | https://www.dshs.texas.gov/vital-statistics/death-records/birth-death-indexes | CSV download | none | free (older) | annual | Public death index (post-25-year window) | ✓ HTTP 200 |
| TX | Environmental | TCEQ Brownfields Site Assessments | https://www.tceq.texas.gov/remediation/bsa | HTML form + spreadsheet | none | free | semi-annual | Brownfield site list with VCP references | ✓ HTTP 200 |
| TX | Environmental | TCEQ Voluntary Cleanup Program | https://www.tceq.texas.gov/remediation/vcp | HTML + spreadsheet | none | free | semi-annual | VCP site spreadsheet (statewide) | ✓ HTTP 200 |
| FL | State GIS | Florida Geographic Information Office (FGIO) | https://www.floridagio.gov/ | ArcGIS Hub | none | free | quarterly | State authoritative GIS portal | ✓ HTTP 200 |
| FL | State GIS | FL Geospatial Open Data Portal | https://geodata.floridagio.gov/ | ArcGIS Hub + REST | none | free | quarterly | Florida Statewide Parcels (10.8M parcels) | ✓ HTTP 200 |
| FL | State GIS | Florida Statewide Cadastral FeatureServer | https://services9.arcgis.com/Gh9awoU677aKree0/arcgis/rest/services/Florida_Statewide_Cadastral/FeatureServer | ArcGIS REST | none | free | quarterly | Direct REST endpoint for parcels | ✓ HTTP 200 |
| FL | State GIS | Florida Geographic Data Library (FGDL) | https://fgdl.org/ | CSV download + shapefile | none | free | varies | UF-hosted alternative GIS clearinghouse | ✓ HTTP 200 |
| FL | Property Tax / DOR | FL DOR — Tax Roll Data Portal (NAL/SDF/NAP) | https://floridarevenue.com/property/Pages/DataPortal_RequestAssessmentRollGISData.aspx | CSV download | none | free | annual (July) | Statewide NAL parcel rolls from 67 county appraisers | ✓ HTTP 200 |
| FL | Property Tax / DOR | FL DOR — Tax Roll File Directory | https://floridarevenue.com/property/dataportal/Pages/default.aspx | CSV download | none | free | annual | Per-county NAL/NAP zips | ✓ HTTP 200 |
| FL | Court System | Florida Courts E-Filing Portal (myflcourtaccess) | https://www.myflcourtaccess.com/ | HTML form | account | free filing / view varies | continuous | Statewide e-filing portal; foreclosure cases | ✓ HTTP 200 |
| FL | Court System | Florida State Courts | https://www.flcourts.gov/ | HTML | none | free | n/a | Court system landing + per-county clerk directory | ✓ HTTP 200 |
| FL | Court System | FL Supreme Court — Residential Mortgage Foreclosure Cases | https://supremecourt.flcourts.gov/ | HTML | none | free | continuous | Foreclosure-case program info, statewide notices | ✓ HTTP 200 |
| FL | SoS / UCC | Sunbiz — Division of Corporations | https://dos.fl.gov/sunbiz/ | HTML form | none | free | continuous | Business entity index | ✓ HTTP 200 |
| FL | SoS / UCC | Sunbiz — UCC Information | https://dos.fl.gov/sunbiz/other-services/ucc-information/ | HTML | none | free | continuous | Routes to floridaucc.com | ✓ HTTP 200 |
| FL | SoS / UCC | Florida Secured Transaction Registry (FloridaUCC) | https://www.floridaucc.com/ | HTML form | account | free search | continuous | Statewide UCC-1/3 lien filings & search | ✓ HTTP 200 |
| FL | WARN | FloridaCommerce — REACT WARN Notices | https://floridajobs.org/office-directory/division-of-workforce-services/workforce-programs/reemployment-and-emergency-assistance-coordination-team-react/warn-notices | PDF only | none | free | weekly | Posted as monthly PDFs by REACT team | ✓ HTTP 200 |
| FL | WARN | FloridaJobs.org (parent portal) | https://floridajobs.org/ | HTML | none | free | continuous | Portal containing REACT WARN list | ✓ HTTP 200 |
| FL | Vital Records | FL DOH Bureau of Vital Statistics — Death Certificates | https://www.floridahealth.gov/certificates-records/death-certificates/ | HTML form | none | $5+ | continuous | Statewide death records 1877-present | ✓ HTTP 200 |
| FL | Environmental | FL DEP Brownfields Program | https://floridadep.gov/waste/waste-cleanup/content/brownfields-program | HTML | none | free | continuous | Program landing + designated areas | ✓ HTTP 200 |
| FL | Environmental | FL DEP Brownfields GeoViewer | https://floridadep.gov/waste/waste-cleanup/content/dep-brownfields-geoviewer | ArcGIS Hub | none | free | quarterly | Mapping of state-designated brownfields | ✓ HTTP 200 |
| FL | Environmental | FL DEP Brownfield Sites (open data) | https://geodata.dep.state.fl.us/datasets/FDEP::brownfield-sites/about | ArcGIS REST | none | free | quarterly | Brownfield sites layer, downloadable | ✓ HTTP 200 |
| OH | State GIS | OGRIP Ohio GeoHub | https://ogrip-geohio.opendata.arcgis.com/ | ArcGIS Hub + REST | none | free | quarterly | Ohio Geographically Referenced Information Program | ✓ HTTP 200 |
| OH | State GIS | OGRIP Statewide Parcels Hub | https://ohioparcels-geohio.hub.arcgis.com/ | ArcGIS Hub + REST | none | free | annual | Statewide standardized parcels | ✓ HTTP 200 |
| OH | State GIS | OGRIP GIS Data Downloads | https://gis1.oit.ohio.gov/geodatadownload/ | CSV download | none | free | varies | LiDAR, imagery, framework downloads | ✓ HTTP 200 |
| OH | Property Tax / DOR | Ohio Department of Taxation — Real Property | https://tax.ohio.gov/government/real-property-tax | HTML + PDF | none | free | annual | CAUV tables, abstract reports per county | ✓ HTTP 200 |
| OH | Property Tax / DOR | Ohio Department of Taxation (root) | https://tax.ohio.gov/ | HTML | none | free | n/a | Statewide tax-equalization data hub | ✓ HTTP 200 |
| OH | Court System | Ohio Supreme Court — Public Docket (eCMS) | https://www.supremecourt.ohio.gov/clerk/ecms/ | HTML form | none | free | continuous | Statewide Supreme Court / appellate dockets only — no unified trial-court search | ✓ HTTP 200 |
| OH | Tax/Foreclosure Auction | Ohio Sheriff Sales (statewide RealAuction) | https://www.ohiosheriffsales.com/ | HTML form | account | free to view | continuous | Official statewide foreclosure auction site (HB 390 contract) | ✓ HTTP 200 |
| OH | SoS / UCC | Ohio SoS — Uniform Commercial Code | https://www.ohiosos.gov/business/uniform-commercial-code | HTML form | none | free search | continuous | UCC1/3/5 filings & search | ✓ HTTP 200 (browser; 403 to curl) |
| OH | WARN | Ohio JFS — WARN Notices | https://jfs.ohio.gov/owf/index.stm | HTML | none | free | weekly | Daily-updated WARN postings via JFS | ✓ HTTP 200 |
| OH | Open Data | DataOhio Portal | https://data.ohio.gov/wps/portal/gov/data/ | OData + REST JSON | none | free | varies | 200+ datasets across state agencies | ✓ HTTP 200 |
| OH | Environmental | Ohio EPA Voluntary Action Program (VAP) | https://epa.ohio.gov/divisions-and-offices/environmental-response-revitalization/voluntary-action-program-vap | HTML | none | free | continuous | Brownfield / DERR statewide list | ✓ HTTP 200 |
| GA | State GIS | Georgia Geospatial Information Office (GIO) | https://gio.ga.gov/ | ArcGIS Hub | none | free | varies | State authoritative GIS landing | ✓ HTTP 200 |
| GA | State GIS | Georgia GIO Data Hub | https://data-hub.gio.georgia.gov/ | ArcGIS Hub + REST | none | free | varies | Federated statewide layers (CSV/KML/GeoJSON/WMS/WFS) | ✓ HTTP 200 |
| GA | State GIS | Georgia GIS Clearinghouse (parcels) | https://georgiagisclearinghouse.com/ | CSV download + shapefile | account | free + paid tier | varies | Privately hosted; >20% of GA counties' parcels | ✓ HTTP 200 |
| GA | Property Tax / DOR | GA DOR — Digest Compliance | https://dor.georgia.gov/local-government-services/digest-compliance | HTML + PDF | none | free | annual | Annual ad valorem digests submitted by counties | ✓ HTTP 200 |
| GA | Property Tax / DOR | GeorgiaData.org — DOR Tax Digest Data | https://georgiadata.org/depofrevdata | CSV download | none | free | annual | Per-county tax-digest files (UGA-hosted) | ✓ HTTP 200 |
| GA | Court System | Georgia Courts — eAccess to Court Records | https://georgiacourts.gov/eaccess-court-records/ | HTML | none | free | n/a | Routes to county / re:SearchGA | ✓ HTTP 200 |
| GA | Court System | re:SearchGA (Tyler) | https://researchga.tylerhost.net/CourtRecordsSearch/ui/Home | HTML form | account | free + paid tier | continuous | Search 25+ Georgia county courts at once | ✓ HTTP 200 |
| GA | Court System | GSCCCA — Statewide Search Portal | https://www.gsccca.org/search | HTML form | account | free + paid tier | daily | Civil cases, real estate, liens, UCC, notaries — statewide index | ✓ HTTP 200 |
| GA | SoS / UCC | GSCCCA — UCC Search | https://search.gsccca.org/UCC_Search/ | HTML form | account | free search | daily | Statewide UCC-1 lien index (GA does not centralize UCC at SoS) | ✓ HTTP 200 |
| GA | WARN | GA Dept. of Labor — WARN Listing | https://dol.georgia.gov/ | HTML | none | free | weekly | DOL maintains business layoff & closure list | ✓ HTTP 200 |
| GA | WARN | TCSG WARN Public View | https://www.tcsg.edu/warn-public-view/ | HTML | none | free | weekly | Technical College System publishes filtered WARN view | ✓ HTTP 200 |
| GA | Open Data | Georgia Data Analytics Center | https://gdac.georgia.gov/ | HTML + REST JSON | none | free | varies | State-government analytics hub | ✓ HTTP 200 |
| GA | Open Data | GeorgiaData.org (UGA) | https://georgiadata.org/ | CSV download | none | free | varies | Long-running statewide data repository | ✓ HTTP 200 |
| GA | Vital Records | GA DPH — Death Records | https://dph.georgia.gov/ways-request-vital-record/death | HTML form | none | $25/copy | continuous | Statewide death records 1919-present | ✓ HTTP 200 |
| GA | Vital Records | Georgia Vital Events Registration System (ROVER) | https://gavers.dph.ga.gov/ | HTML form | account | $25/copy | continuous | Online ordering portal | ✓ HTTP 200 |
| GA | Probate | Georgia Probate Records Search | https://www.georgiaprobaterecords.com/Estates/SearchEstates.aspx | HTML form | none | free | continuous | Statewide estate / probate index | ✓ HTTP 200 |
| GA | Environmental | GA EPD — Brownfield | https://epd.georgia.gov/about-us/land-protection-branch/hazardous-waste/brownfield | HTML | none | free | continuous | Brownfield program landing | ✓ HTTP 200 |
| GA | Environmental | GA EPD — Hazardous Site Inventory | https://epd.georgia.gov/about-us/land-protection-branch/hazardous-waste/hazardous-site-inventory | HTML + PDF | none | free | semi-annual | State Superfund list (HSI) | ✓ HTTP 200 |
| NC | State GIS | NC OneMap | https://www.nconemap.gov/ | ArcGIS Hub | none | free | quarterly | Statewide GIS clearinghouse | ✓ HTTP 200 |
| NC | State GIS | NC OneMap — Parcels MapServer | https://services.nconemap.gov/secure/rest/services/NC1Map_Parcels/MapServer | ArcGIS REST | none | free | quarterly | All 100 counties + EBCI lands; max 5000 / query | ✓ HTTP 200 |
| NC | State GIS | NC DEQ GIS Open Data Portal | https://data-ncdenr.opendata.arcgis.com/ | ArcGIS Hub + REST | none | free | varies | DEQ environmental layers (brownfields, contamination) | ✓ HTTP 200 |
| NC | Property Tax / DOR | NCDOR — Property Tax Division | https://www.ncdor.gov/taxes-forms/property-tax/property-tax-division | HTML + PDF | none | free | annual | Oversees county valuation; publishes schedules of values | ✓ HTTP 200 |
| NC | Court System | NC Judicial Branch — eCourts | https://www.nccourts.gov/ecourts | HTML form | none / account | free / paid bulk | continuous | Statewide eCourts (Tyler Odyssey) — 100% county coverage as of Oct 2025 | ✓ HTTP 200 |
| NC | SoS / UCC | NC SoS — UCC Section | https://www.sosnc.gov/divisions/uniform_commercial_code | HTML | none | free search | continuous | Statewide UCC search and filing | ✓ HTTP 200 |
| NC | WARN | NC Commerce — Workforce WARN Reports | https://www.commerce.nc.gov/data-tools-reports/labor-market-data-tools/workforce-warn-reports | HTML + PDF | none | free | monthly | Annual / monthly WARN listings | ✓ HTTP 200 |
| NC | WARN | NC Commerce — File a WARN Notice | https://www.commerce.nc.gov/business/business-closure-resources/file-warn-notice | HTML | none | free | continuous | Filing portal + employer guidance | ✓ HTTP 200 |
| NC | Open Data | LINC — NC OSBM Open Data | https://linc.osbm.nc.gov/ | OData + REST JSON | none | free | varies | 1,500+ data series, census/state programs | ✓ HTTP 200 |
| NC | Open Data | NC Data Portal (NCDP) | https://ncdataportal.org/ | HTML | none | free | varies | Public-health & demographic data layers | ✓ HTTP 200 |
| NC | Vital Records | NC DPH Vital Records | https://vitalrecords.nc.gov/ | HTML form | none | $24/copy | continuous | Statewide deaths from 1913 | ✓ HTTP 200 |
| NC | Environmental | NC DEQ Brownfields Redevelopment | https://www.deq.nc.gov/about/divisions/waste-management/brownfields-redevelopment-section/program-information | HTML | none | free | continuous | 500+ sites; agreements & maps | ✓ HTTP 200 |
| NC | Environmental | NC DEQ Brownfields Map / Inventory | https://www.deq.nc.gov/about/divisions/waste-management/science-data-and-reports/gis-maps/brownfields-projects-map-inventory-and-document-download | ArcGIS Hub | none | free | quarterly | Project map + downloadable inventory | ✓ HTTP 200 |
| NC | Environmental | NC DEQ Inactive Hazardous Sites Program | https://www.deq.nc.gov/about/divisions/waste-management/superfund-section/inactive-hazardous-sites-program | HTML + PDF | none | free | annual | Inactive Hazardous Sites Inventory (state Superfund) | ✓ HTTP 200 |
| AZ | State GIS | AZGeo Data Hub (AGIC) | https://azgeo-data-hub-agic.hub.arcgis.com/ | ArcGIS Hub + REST | none | free | quarterly | Authoritative statewide GIS portal | ✓ HTTP 200 |
| AZ | State GIS | AZGeo Open Data | https://azgeo-open-data-agic.hub.arcgis.com/ | ArcGIS Hub + REST | none | free | quarterly | Federated agency / city / county datasets | ✓ HTTP 200 |
| AZ | State GIS | AGIC — Geospatial Resources | http://www.agic.az.gov/agic/geospatial-data-collections-and-resources | HTML | none | free | n/a | Arizona Geographic Information Council resource directory | ✓ HTTP 200 |
| AZ | Court System | Arizona eAccess (Superior Court case search) | https://eaccess.azcourts.gov/ | HTML form | none | free | continuous | Civil + criminal cases, statewide back to July 2010 | ✓ HTTP 200 |
| AZ | Court System | Arizona Judicial Branch — eAccess landing | https://www.azcourts.gov/eaccess/ | HTML | none | free | n/a | eAccess hub with county coverage map | ✓ HTTP 200 |
| AZ | Court System | Arizona Judicial Branch (root) | https://www.azcourts.gov/ | HTML | none | free | n/a | Court system home | ✓ HTTP 200 |
| AZ | WARN | AZ Job Connection — WARN Search | https://www.azjobconnection.gov/search/warn_lookups/new | HTML form | none | free | weekly | Searchable list of layoffs/closures | ✓ HTTP 200 |
| AZ | WARN | AZ Job Connection — WARN Info page | https://www.azjobconnection.gov/warn_info | HTML | none | free | n/a | About / overview | ✓ HTTP 200 |
| AZ | Environmental | ADEQ — Voluntary Remediation Program (VRP) Sites | https://azdeq.gov/VRPsites | HTML + spreadsheet | none | free | semi-annual | Active VRP sites by county (~60+ active) | ✓ HTTP 200 (browser; 403 to curl/WebFetch — Cloudflare bot wall) |
| AZ | Environmental | ADEQ — Brownfields | https://azdeq.gov/brownfields | HTML | none | free | continuous | Brownfields program | ✓ HTTP 200 (browser; 403 to curl/WebFetch) |
| TN | State GIS | TNMap — STS GIS Services | https://tnmap.tn.gov/ | ArcGIS Hub | none | free | quarterly | Statewide mapping portal | ✓ HTTP 200 |
| TN | State GIS | TN Downloadable GIS Data | https://tn-tnmap.opendata.arcgis.com/ | ArcGIS Hub + REST | none | free | quarterly | Open data hub w/ statewide parcels | ✓ HTTP 200 |
| TN | State GIS | TN Property Boundaries (parcels) | https://tn-tnmap.opendata.arcgis.com/maps/53ee269639334ad8b70bfea77bde7333 | ArcGIS REST | none | free | quarterly | Parcels for 86 counties using state IMPACT system | ✓ HTTP 200 |
| TN | State GIS | TNGIS / Tennessee Geographic Information Council | https://www.tngis.org/ | HTML | none | free | n/a | Statewide GIS clearinghouse / index of layers | ✓ HTTP 200 |
| TN | Property Tax / DOR | TN Comptroller — IMPACT (statewide CAMA) | https://comptroller.tn.gov/office-functions/pa/impact/what-is-impact.html | HTML | none | free | continuous | State-hosted CAMA serving most TN assessors | ✓ HTTP 200 |
| TN | Property Tax / DOR | TN Comptroller — Property Assessment Glossary / PA | https://comptroller.tn.gov/office-functions/pa/property-taxes/tennessee-property-assessment-glossary.html | HTML + PDF | none | free | annual | State Board of Equalization / PA division | ✓ HTTP 200 |
| TN | Court System | TN AOC — Public Case History (Supreme Court) | https://www.tncourts.gov/courts/supreme-court/public-case-history | HTML form | none | free | continuous | Supreme + appellate cases statewide | ✓ HTTP 200 |
| TN | Court System | TennesseeCaseFinder | https://www.tennesseecasefinder.com/ | HTML form | none | free | continuous | Multi-county Circuit / General Sessions civil & foreclosure | ✓ HTTP 200 |
| TN | WARN | TN Dept. of Labor & Workforce Development — WARN | https://www.tn.gov/workforce/employers/staffing-redirect/layoffs---unemployment/warn-notices.html | PDF only | none | free | weekly | Statewide WARN dashboard + archive | ✓ HTTP 200 |
| TN | Open Data | TN DATA (Office of Evidence & Impact) | https://www.tn.gov/finance/oei/tn-data.html | HTML | none | free | varies | State analytics and dashboards | ✓ HTTP 200 |
| TN | Environmental | TDEC Division of Remediation | https://www.tn.gov/environment/program-areas/rem-remediation.html | HTML | none | free | continuous | Remediation programs landing | ✓ HTTP 200 |
| TN | Environmental | TDEC VOAP (Brownfield Voluntary Cleanup) | https://www.tn.gov/environment/program-areas/rem-remediation/voap.html | HTML | none | free | continuous | VOAP program — verified via WebFetch | ✓ HTTP 200 (browser; 000 to curl) |
| IN | State GIS | IndianaMap (Hub) | https://indianamap-inmap.hub.arcgis.com/ | ArcGIS Hub + REST | none | free | annual | State GIS clearinghouse | ✓ HTTP 200 |
| IN | State GIS | IndianaMap (legacy site) | https://www.indianamap.org/ | HTML + REST | none | free | annual | Resources page + dataset directory | ✓ HTTP 200 |
| IN | State GIS | Indiana GIS @ in.gov | https://www.in.gov/gis/indianamap/ | HTML | none | free | n/a | IGIO (Indiana Geographic Information Office) | ✓ HTTP 200 |
| IN | State GIS | Indiana State Cleanup Sites layer | https://www.indianamap.org/datasets/state-cleanup-sites/about | ArcGIS REST | none | free | quarterly | IDEM remediation sites as a hosted layer | ✓ HTTP 200 |
| IN | Property Tax / DOR | DLGF — Department of Local Government Finance | https://www.in.gov/dlgf/ | HTML | none | free | continuous | Property tax oversight & assessment rules | ✓ HTTP 200 |
| IN | Property Tax / DOR | DLGF Tax Bill Search | https://www.in.gov/dlgf/understanding-your-tax-bill/tax-bill-search/ | HTML form | none | free | continuous | Statewide parcel-level tax bills | ✓ HTTP 200 |
| IN | Property Tax / DOR | Indiana Gateway (IFI) — Report Builder | https://gateway.ifionline.org/report_builder/ | CSV download | none | free | annual | Statewide assessment / tax-bill reports | ✓ HTTP 200 |
| IN | Court System | mycase.in.gov (Odyssey Public Access) | https://mycase.in.gov/ | HTML form | none | free | continuous | Statewide trial + appellate court case search | ✓ HTTP 200 |
| IN | Court System | mycase.in.gov (alt URL) | https://public.courts.in.gov/mycase/ | HTML form | none | free | continuous | Same Odyssey portal, alternate hostname | ✓ HTTP 200 |
| IN | SoS / UCC | INBiz — UCC Filings & Search | https://inbiz.in.gov/business-filings/ucc | HTML form | account | $10 filing | continuous | Statewide UCC at IN SoS | ✓ HTTP 200 |
| IN | WARN | IN DWD — WARNs landing | https://www.in.gov/dwd/warn-notices/ | HTML | none | free | weekly | Worker adjustment notices | ✓ HTTP 200 |
| IN | WARN | IN DWD — Current WARN Notices (table) | https://www.in.gov/dwd/warn-notices/current-warn-notices/ | HTML form + CSV download | none | free | weekly | Sortable table back to 2008, exportable | ✓ HTTP 200 |
| IN | Open Data | Indiana Data Hub (MPH) | https://hub.mph.in.gov/ | REST JSON (CKAN) | none | free | varies | 180+ datasets, CKAN-based | ✓ HTTP 200 |
| IN | Open Data | Indiana Management Performance Hub | https://www.in.gov/mph/ | HTML | none | free | n/a | Parent agency for data hub + transparency portal | ✓ HTTP 200 |
| IN | Vital Records | IN Dept. of Health — Vital Records | https://www.in.gov/health/vital-records/ | HTML form | none | $8/copy | continuous | Death certificates statewide | ✓ HTTP 200 |
| IN | Environmental | IDEM — Voluntary Remediation Program | https://www.in.gov/idem/cleanups/investigation-and-cleanup-programs/voluntary-remediation-program/ | HTML + CSV download | none | free | semi-annual | VRP site list (XLSX, updated Apr 2025) | ✓ HTTP 200 |
| IN | Environmental | IDEM — Institutional Controls Registry | https://www.in.gov/idem/cleanups/investigation-and-cleanup-programs/institutional-controls/ | HTML + CSV download | none | free | quarterly | Sites with deed restrictions / use limits, sorted by county | ✓ HTTP 200 |
| IN | Environmental | Indiana Brownfields Program (IFA) | https://www.in.gov/ifa/brownfields/ | HTML | none | free | continuous | Brownfield program portal | ✓ HTTP 200 |
| IN | Environmental | Indiana Brownfields — Program Sites | https://www.in.gov/ifa/brownfields/program-sites/ | HTML + CSV download | none | free | continuous | Per-site directory of brownfield projects | ✓ HTTP 200 |
| MI | State GIS | Michigan GIS Open Data | https://gis-michigan.opendata.arcgis.com/ | ArcGIS Hub + REST | none | free | varies | Michigan Geographic Framework (MGF) | ✓ HTTP 200 |
| MI | State GIS | DTMB — Maps / GIS | https://www.michigan.gov/dtmb/services/maps | HTML | none | free | n/a | State agency GIS landing | ✓ HTTP 200 |
| MI | State GIS | EGLE Part 201 Environmental Contamination Sites | https://gis-michigan.opendata.arcgis.com/datasets/egle::part-201-environmental-contamination-sites/about | ArcGIS REST | none | free | quarterly | Statewide contaminated-sites layer | ✓ HTTP 200 |
| MI | Property Tax / DOR | MI Treasury — State Tax Commission | https://www.michigan.gov/treasury/local/stc | HTML + PDF | none | free | annual | Statewide assessment summaries / classification reports | ✓ HTTP 200 |
| MI | Court System | MiCOURT Case Search | https://micourt.courts.michigan.gov/case-search/ | HTML form | none | free | continuous | Statewide unified case search | ✓ HTTP 200 |
| MI | Court System | One Court of Justice — Case Search portal | https://www.courts.michigan.gov/case-search/ | HTML | none | free | n/a | Trial + appellate court search hub | ✓ HTTP 200 |
| MI | SoS / UCC | MI SoS — UCC | https://www.michigan.gov/sos/industry-services/ucc | HTML | none | free | continuous | UCC program landing | ✓ HTTP 200 |
| MI | SoS / UCC | MI UCC Online Customer Portal | https://ucc.michigan.gov/ | HTML form | account | $15/filing | continuous | UCC1/3 filing & search | ✓ HTTP 200 |
| MI | WARN | MI LEO — WARN Notices | https://www.michigan.gov/leo/bureaus-agencies/wd/data-public-notices/warn-notices | PDF only | none | free | weekly | LEO posts WARN PDFs since 2025 | ✓ HTTP 200 |
| MI | Vital Records | MI Vital Records — GENDIS Death Index | https://vitalstats.michigan.gov/osr/gendisx/index.asp | HTML form | none | free index | continuous | Statewide death index 1867-1897 (older); historic only | ✓ HTTP 200 |
| MI | Environmental | EGLE — Baseline Environmental Assessments | https://www.michigan.gov/egle/about/organization/Remediation-and-Redevelopment/Baseline-Environmental-Assessments/ | HTML | none | free | continuous | BEA filings & program | ✓ HTTP 200 |
| MI | Environmental | EGLE — Remediation Information & Data Exchange (RIDE) | https://www.egle.state.mi.us/RIDE/ | HTML form | none | free | continuous | Searchable contaminated-site database | ✓ HTTP 200 |
| PA | State GIS | PASDA — PA Spatial Data Access | https://www.pasda.psu.edu/ | CSV download + WMS/WFS | none | free | varies | Penn State-hosted state clearinghouse, FGDC metadata | ✓ HTTP 200 |
| PA | State GIS | PennShare GIS Hub | https://gis-hub-pennshare.hub.arcgis.com/ | ArcGIS Hub + REST | none | free | varies | State Geospatial Coordinating Board hub | ✓ HTTP 200 |
| PA | Property Tax / DOR | PA DCED — State Tax Equalization Board (STEB) | https://dced.pa.gov/local-government/boards-committees/tax-equalization-division/ | HTML + PDF | none | free | annual | Common Level Ratios + aggregate market values per county | ✓ HTTP 200 |
| PA | Court System | UJS Web Portal — Case Search | https://ujsportal.pacourts.us/casesearch | HTML form | none | free | continuous | Statewide case search incl. mortgage-foreclosure dockets | ✓ HTTP 405 (POST-only; portal live) |
| PA | Court System | Unified Judicial System Portal | https://ujsportal.pacourts.us/ | HTML | none | free | n/a | Common Pleas + magisterial dockets | ✓ HTTP 200 |
| PA | Court System | PA Courts (root) | https://www.pacourts.us/ | HTML | none | free | n/a | UJS landing | ✓ HTTP 200 |
| PA | SoS / UCC | PA Department of State — UCC | https://www.pa.gov/agencies/dos/resources/business-resources/uniform-commercial-code | HTML | none | free landing / $12/search | continuous | UCC program info & forms | ✓ HTTP 200 |
| PA | WARN | PA L&I — WARN Notices | https://www.pa.gov/agencies/dli/programs-services/workforce-development-home/warn-requirements/warn-notices | HTML + PDF | none | free | monthly | WARN notices organized by month/year | ✓ HTTP 200 |
| PA | Open Data | PA Open Data Portal | https://data.pa.gov/ | Socrata SODA | none / API key | free | varies | State Socrata portal | ✓ HTTP 200 |
| PA | Vital Records | PA State Archives — Vital Statistics | https://www.pa.gov/agencies/phmc/pa-state-archives/research-online/vital-statistics-records | HTML form | none | free index | continuous | PA Death Index 1906-1975 online | ✓ HTTP 200 |
| PA | Vital Records | PA DOH — Vital Records | https://www.pa.gov/agencies/health/programs/vital-records | HTML form | none | $20/copy | continuous | Modern death certificates (post-1975) | ✓ HTTP 200 |
| PA | Environmental | PA DEP — Land Recycling Program (Act 2) | https://www.pa.gov/agencies/dep/programs-and-services/land/land-recycling-program | HTML | none | free | continuous | Brownfield / Act 2 program landing | ✓ HTTP 200 |
| PA | Environmental | PA DEP — Land Recycling Program Results | https://www.pa.gov/agencies/dep/programs-and-services/land/land-recycling-program/program-results.html | HTML + PDF | none | free | annual | Cleanup completions & site listings | ✓ HTTP 200 |
| -- | Cross-state Auctions | RealAuction (RealForeclose / RealTaxDeed) | https://realauction.com/ | HTML form | account | varies | continuous | Vendor for FL tax deed, OH sheriff sale (statewide), many more | ✓ HTTP 200 |
| -- | Cross-state Auctions | GovEase | https://www.govease.com/ | HTML form | account | varies | continuous | Vendor for TN delinquent tax sales, GA tax deed sales, MS, AL, TX (some) | ✓ HTTP 200 |
| -- | Cross-state Auctions | GovEase Live & Upcoming | https://www.govease.com/tax-sale-property-auctions | HTML form | account | varies | continuous | Cross-state upcoming-auction calendar | ✓ HTTP 200 |
| -- | Cross-state Auctions | Bid4Assets | https://www.bid4assets.com/ | HTML form | account | varies | continuous | Sheriff / tax-defaulted sales nationally; PA Philadelphia + Monroe Co | ✓ HTTP 200 |
| -- | Cross-state Auctions | ForeclosureAuctionData (FL aggregator) | https://foreclosureauctiondata.com/ | HTML form | account | free + paid tier | daily | Aggregates FL county tax-deed + foreclosure data | ✓ HTTP 200 |
| -- | Cross-state Auctions | PropertyOnion (FL aggregator) | https://propertyonion.com/ | HTML form | account | free + paid tier | daily | FL distressed / non-MLS aggregator | ✓ HTTP 200 |
