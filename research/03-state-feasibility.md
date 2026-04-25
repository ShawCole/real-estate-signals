# Phase 1.3 — State-by-State Scraper Feasibility (Top 10 Investor Markets)

**Scope:** TX, FL, OH, GA, NC, AZ, TN, IN, MI, PA. For each state we map foreclosure mechanics, tax-sale mechanics, top-3 investor counties (with portal URLs and tech-stack notes), code-enforcement transparency, probate access, statewide bulk parcel availability, and a go/no-go verdict for MVP scraper investment.

**Scraper-friendliness scale (1-5):**
- 1 = scanned PDFs, CAPTCHAs, paywalls, in-person only
- 2 = clunky HTML form, no pagination contract, brittle
- 3 = stable HTML form with predictable URL params
- 4 = ArcGIS REST / Tyler Public Search / Socrata / OData JSON
- 5 = clean documented JSON API or full bulk CSV/SHP download

---

## TX — Texas

1. **Foreclosure system.** **Non-judicial** (power-of-sale via deed of trust). Timeline can be ~60 days from default to sale. Servicer must record a **Notice of Substitute Trustee's Sale** with the **county clerk** at least 21 days before the sale, post it on the courthouse door, and post online. Sales happen the first Tuesday of each month.
2. **Tax sales.** **Tax-deed state.** Counties hold monthly delinquent-tax sales conducted by **constables** (Harris, Tarrant) or sheriff (smaller counties). Lists posted by county tax office or via vendor [`countytaxsaleapp.org`](https://countytaxsaleapp.org/). 6-month / 2-year statutory redemption.
3. **Top 3 counties.**
   - **Harris (Houston)** — Appraisal district at [HCAD](https://hcad.org) with downloadable bulk files at [`public.hcad.org/records/`](https://public.hcad.org/records/); clerk recording at [`cclerk.hctx.net`](https://www.cclerk.hctx.net) (Tyler-style portal); tax sales [`hctax.net/Property/listings/taxsalelisting`](https://www.hctax.net/Property/listings/taxsalelisting). **4/5** — bulk data + structured search.
   - **Dallas** — Clerk on **Kofile/PublicSearch** at [`dallas.tx.publicsearch.us`](https://dallas.tx.publicsearch.us/) (NEW Feb-2026 platform); appraisal at DCAD; foreclosure notices listed at [dallascounty.org](https://www.dallascounty.org/government/county-clerk/recording/foreclosures.php). **4/5**.
   - **Tarrant (Fort Worth)** — Clerk on Kofile/PublicSearch at [`tarrant.tx.publicsearch.us`](https://tarrant.tx.publicsearch.us/); TAD parcel search + an **ArcGIS open-data hub** at [`gis-tad.opendata.arcgis.com`](https://gis-tad.opendata.arcgis.com/). **4/5**.
4. **Code enforcement.** Houston open data at [`data.houstontx.gov`](https://data.houstontx.gov/) (300+ layers, Socrata-like); Dallas open-data hub on ArcGIS. Mid coverage.
5. **Probate.** Harris County Probate Courts have an online docket at [`cclerk.hctx.net`](https://www.cclerk.hctx.net/applications/websearch/CourtSearch.aspx?CaseType=Probate); statewide **Re:SearchTX** covers most large counties.
6. **Bulk parcel.** No statewide parcel layer, but the largest CADs (HCAD, DCAD, TAD) publish bulk files free. State portal at [`data.texas.gov`](https://data.texas.gov/).
7. **Verdict — GREENLIGHT.** Power-of-sale recordings are uniform across all 254 counties (same statutory notice), and the three biggest CADs publish bulk parcel files. The only friction is fragmented CAD APIs.

---

## FL — Florida

1. **Foreclosure system.** **Judicial only.** Lender files **lis pendens** with the county clerk after 120 days delinquent; full foreclosure averages 480 days. The lis pendens is the highest-value early-warning signal in any state.
2. **Tax sales.** **Tax-lien state first, then tax-deed.** Tax certificates auctioned annually (June) by the **county tax collector**; after 2 years a certificate-holder may force a tax-deed sale. Most counties run online auctions via **RealAuction** or **GovEase**.
3. **Top 3 counties.**
   - **Miami-Dade** — Clerk at [`miamidadeclerk.gov`](https://www.miamidadeclerk.gov); GIS open-data hub at [`gis-mdc.opendata.arcgis.com`](https://gis-mdc.opendata.arcgis.com/) with downloadable parcel + code-violation layers. **5/5** — true open data.
   - **Broward** — Property appraiser at [`bcpa.net`](https://bcpa.net/) (own search UI, no public API); clerk site separate. **3/5**.
   - **Hillsborough (Tampa)** — Clerk's Official Records Public Search at [`publicaccess.hillsclerk.com/oripublicaccess/`](https://publicaccess.hillsclerk.com/oripublicaccess/) — Liferay-based, scrapeable; code-violation search at [`hcfl.gov`](https://hcfl.gov/residents/property-owners-and-renters/code-violations/search-code-violation-cases). **4/5**.
4. **Code enforcement.** Miami-Dade publishes code violations as an ArcGIS feature service; Tampa uses **Accela Citizen Access**; Hillsborough has its own search UI. Strong overall.
5. **Probate.** Each county clerk runs an online docket; quality varies by county.
6. **Bulk parcel.** **Excellent.** Florida DOR ships an annual statewide assessment-roll download; [`geodata.floridagio.gov`](https://geodata.floridagio.gov/) and FGDL ([`fgdl.org`](https://fgdl.org/)) host statewide parcel layers.
7. **Verdict — GREENLIGHT.** Best statewide bulk parcel of any state in this set, judicial foreclosure means lis pendens are filed centrally, and 3 biggest counties have ArcGIS or stable HTML portals. Top tier.

---

## OH — Ohio

1. **Foreclosure system.** **Judicial.** Lender files complaint in **Court of Common Pleas**; borrower has 28 days to answer, then sheriff's sale (now mostly online via [`ohiosheriffsales.com`](https://www.ohiosheriffsales.com/)). 60-day sheriff confirmation window after sale.
2. **Tax sales.** **Tax-lien certificate state** under ORC §5721.30-43. Counties bundle delinquent parcels and sell certificates to institutional buyers (Cuyahoga, Franklin, Hamilton); few "over-the-counter" individual lien purchases. Forfeited Land Sales also exist.
3. **Top 3 counties.**
   - **Cuyahoga (Cleveland)** — Fiscal Officer's **MyPlace** + GIS Hub at [`fiscalhub.gis.cuyahogacounty.gov`](https://fiscalhub.gis.cuyahogacounty.gov/); recorder on PublicSearch at [`cuyahoga.oh.publicsearch.us`](https://cuyahoga.oh.publicsearch.us/); city open-data at [`data.clevelandohio.gov`](https://data.clevelandohio.gov/). **5/5** — exceptional.
   - **Franklin (Columbus)** — Auditor at [`property.franklincountyauditor.com`](https://property.franklincountyauditor.com/) on a stable parcel-ID URL pattern; ArcGIS hub at [`auditor-fca.opendata.arcgis.com`](https://auditor-fca.opendata.arcgis.com/); code enforcement on [`opendata.columbus.gov`](https://opendata.columbus.gov/). **5/5**.
   - **Hamilton (Cincinnati)** — Auditor "Wedge" at [`wedge.hcauditor.org`](https://wedge.hcauditor.org/); recorder uses **Acclaim from Harris** at [`acclaim-web.hamiltoncountyohio.gov`](https://acclaim-web.hamiltoncountyohio.gov/AcclaimWebLive); code-violation dashboards on Cincinnati's Tyler/Socrata data portal. **4/5**.
4. **Code enforcement.** Best in the country for this set. Columbus + Cincinnati publish code-enforcement cases as live datasets. Cleveland exposes vacancy/abandonment metrics openly.
5. **Probate.** Each county Court of Common Pleas Probate Division has an online docket; quality varies; Cuyahoga and Franklin are strong.
6. **Bulk parcel.** No single statewide bulk file, but Cuyahoga/Franklin/Hamilton each publish parcel and sales feeds via their auditor open-data hubs.
7. **Verdict — GREENLIGHT.** Sheriff's-sale info is uniform statewide, the three biggest counties all expose ArcGIS and structured search, and code-enforcement open data is unmatched. Top tier.

---

## GA — Georgia

1. **Foreclosure system.** **Non-judicial.** Notice published in the official county newspaper for **4 consecutive weeks**; sale on the first Tuesday of the month on the courthouse steps. The newspaper-only notice requirement is a real scraping headache because notices are not centrally indexed.
2. **Tax sales.** **Tax-deed state with redeemable deeds** (1-year redemption at 20% premium). The **tax commissioner acts as ex-officio sheriff** and conducts monthly Fi.Fa. (fieri facias) sales.
3. **Top 3 counties.**
   - **Fulton (Atlanta)** — Assessor on **Schneider qPublic** at [`qpublic.schneidercorp.com/Application.aspx?App=FultonCountyGA`](https://qpublic.schneidercorp.com/Application.aspx?App=FultonCountyGA&Layer=Parcels&PageType=Search); IAS at [`iaspublicaccess.fultoncountyga.gov`](https://iaspublicaccess.fultoncountyga.gov/). **3/5** (qPublic is heavy JavaScript).
   - **Gwinnett** — Assessor on qPublic; tax commissioner at [`gwinnetttaxcommissioner.com`](https://www.gwinnetttaxcommissioner.com/); ArcGIS open-data hub at [`gcgis-gwinnettcountyga.hub.arcgis.com`](https://gcgis-gwinnettcountyga.hub.arcgis.com/). **4/5**.
   - **Cobb (Marietta)** — Assessor on qPublic + own GIS at [`gis.cobbcounty.org/PropertySearch.html`](https://gis.cobbcounty.org/PropertySearch.html); ArcGIS parcel viewer. **4/5**.
4. **Code enforcement.** Atlanta has open data; suburban counties spotty. Largely FOIA territory.
5. **Probate.** Each county Probate Court runs its own portal; Georgia has **no unified court e-filing** for probate.
6. **Bulk parcel.** **GIS1 / Georgia GIS Clearinghouse** at [`georgiagisclearinghouse.com`](https://georgiagisclearinghouse.com/) covers ~20% of counties; remaining counties via individual GIS hubs.
7. **Verdict — YELLOW.** qPublic covers most counties (one scraper, many counties), but the newspaper-only foreclosure-notice rule means we'd need a publication-level scraper or partner with [`legals.georgiapublicnotice.com`](https://legals.georgiapublicnotice.com). Real but doable.

---

## NC — North Carolina

1. **Foreclosure system.** **Power-of-sale with clerk hearing.** Trustee files **notice of hearing** with the **clerk of superior court**; clerk authorizes sale. After sale there is a 10-day **upset-bid period**. Hearings are docketed in the eCourts/ACIS system.
2. **Tax sales.** **Tax-deed state via judicial in rem foreclosure** under N.C.G.S. §105-374. County tax collector hires private attorneys to file foreclosures; 10-day upset-bid period applies.
3. **Top 3 counties.**
   - **Mecklenburg (Charlotte)** — **POLARIS** GIS at [`polaris3g.mecklenburgcountync.gov`](https://polaris3g.mecklenburgcountync.gov/) (open & comprehensive); register of deeds at [`deeds.mecknc.gov`](https://deeds.mecknc.gov/); foreclosure list at [tax.mecknc.gov](https://tax.mecknc.gov/services/tax-foreclosure-properties). **5/5**.
   - **Wake (Raleigh)** — Tax search at [`services.wake.gov/realestate/`](https://services.wake.gov/realestate/); **Books! ROD search** at [`rodcrpi.wakegov.com/booksweb/`](https://rodcrpi.wakegov.com/booksweb/); open data on ArcGIS at [`data-wake.opendata.arcgis.com`](https://data-wake.opendata.arcgis.com/). **5/5**.
   - **Guilford (Greensboro)** — ROD at [`rdlxweb.guilfordcountync.gov`](https://rdlxweb.guilfordcountync.gov/); tax property search via NCPTS Cloud at [`lrcpwa.ncptscloud.com/guilford/`](https://lrcpwa.ncptscloud.com/guilford/). **3/5**.
4. **Code enforcement.** Charlotte and Raleigh publish open data; smaller cities require FOIA.
5. **Probate.** Statewide on **NC eCourts** (rolling out via Tyler Odyssey) — improving rapidly.
6. **Bulk parcel.** **NC OneMap** at [`nconemap.gov/pages/parcels`](https://www.nconemap.gov/pages/parcels) ships a single statewide standardized parcel geodatabase — best-in-class.
7. **Verdict — GREENLIGHT.** Strong statewide bulk parcel, clerk-of-court hearing creates a centrally-indexed pre-foreclosure trigger, and the top counties are open-data leaders.

---

## AZ — Arizona

1. **Foreclosure system.** **Non-judicial trustee sale** (ARS §33-808). **Notice of Trustee's Sale** recorded with the **county recorder**, posted on property, published 4 weeks. Sale earliest 91 days after recording.
2. **Tax sales.** **Tax-lien certificate state.** Annual online auction in February. Maricopa runs its own platform [`treasurer.maricopa.gov/taxlienweb`](https://treasurer.maricopa.gov/taxlienweb/) with downloadable monthly statements; bidders bid down interest rate (16% → 0%). 3-year minimum redemption before judicial foreclosure.
3. **Top 3 counties.**
   - **Maricopa (Phoenix)** — Treasurer at [`treasurer.maricopa.gov`](https://treasurer.maricopa.gov/); assessor + recorder portals (recorder is searchable HTML). Strong tax-lien data flow. **4/5**.
   - **Pima (Tucson)** — Assessor at [`asr.pima.gov`](https://www.asr.pima.gov/); recorder Public Search at [`recorder.pima.gov/PublicSearch`](https://www.recorder.pima.gov/PublicSearch); GIS parcel at [`gis.pima.gov/maps/landbase/parsrch.htm`](https://gis.pima.gov/maps/landbase/parsrch.htm). **4/5**.
   - **Pinal** — Assessor parcel search at [`pinal.gov/686/Parcel-Search`](https://www.pinal.gov/686/Parcel-Search); recorder uses **AcclaimWeb (Harris)** at [`acclaim.pinalcountyaz.gov/AcclaimWeb/`](https://acclaim.pinalcountyaz.gov/AcclaimWeb/). **4/5**.
4. **Code enforcement.** Phoenix and Tucson have open-data portals; suburban municipalities spotty.
5. **Probate.** Statewide Arizona Judicial Branch portal; Maricopa Superior Court has online dockets.
6. **Bulk parcel.** **AZGeo Data Hub** at [`azgeo-data-hub-agic.hub.arcgis.com`](https://azgeo-data-hub-agic.hub.arcgis.com/) — state aggregates from counties. Maricopa publishes its own ArcGIS parcel feature service.
7. **Verdict — GREENLIGHT.** Only 15 counties in the whole state, all 3 majors expose structured portals, and the Maricopa Tax Lien Web is one of the cleanest treasury data feeds in the country.

---

## TN — Tennessee

1. **Foreclosure system.** Mostly **non-judicial** (power-of-sale). Notice published 3+ times in a county newspaper, first publication 20+ days pre-sale; trustee also files notice with the **Secretary of State**. The Secretary of State filing is the only state-level centralization in this set.
2. **Tax sales.** **Tax-deed state with 1-year redemption.** Conducted by Chancery Court Clerk & Master (Davidson uses [Chancery Clerk and Master](https://chanceryclerkandmaster.nashville.gov/fees/delinquent-tax-sales/); Shelby uses **ZeusAuction.com**).
3. **Top 3 counties.**
   - **Davidson (Nashville)** — Property assessor at [`padctn.org`](https://www.padctn.org/) (own search, real-property search has been flaky per assessor's notice); tax via [`nashville-tn.mygovonline.com`](https://nashville-tn.mygovonline.com/mod.php?mod=propertytax&mode=public_lookup). **3/5**.
   - **Shelby (Memphis)** — Assessor [`assessormelvinburgess.com/propertySearch`](https://www.assessormelvinburgess.com/propertySearch); register of deeds GIS at [`gis.register.shelby.tn.us`](https://gis.register.shelby.tn.us/) and search at [`search.register.shelby.tn.us`](https://search.register.shelby.tn.us/search/index.php); tax sales via Zeus. **4/5**.
   - **Knox (Knoxville)** — ROD at [`rod.knoxcounty.org`](https://rod.knoxcounty.org/) and Public Access at [`paxsub.knoxrod.org`](https://paxsub.knoxrod.org/); property at [`propertyinfo.knoxcountytn.gov`](https://propertyinfo.knoxcountytn.gov/). **4/5**.
4. **Code enforcement.** Nashville has open data ([data.nashville.gov](https://data.nashville.gov)); other cities mostly FOIA.
5. **Probate.** County Chancery / Probate Courts; quality varies.
6. **Bulk parcel.** **State Comptroller** offers per-county shapefile downloads at [comptroller.tn.gov](https://comptroller.tn.gov/office-functions/pa/gisredistricting/redistricting-and-land-use-maps/parcel-data.html); state geodata at [`tn-tnmap.opendata.arcgis.com`](https://tn-tnmap.opendata.arcgis.com/). Solid.
7. **Verdict — YELLOW.** The Secretary-of-State filing is a genuine asset (single-source pre-foreclosure feed), but newspaper-only sale notices and Davidson's assessor instability bring down the score. Worth scraping the SoS feed first; defer county-level until volume justifies it.

---

## IN — Indiana

1. **Foreclosure system.** **Judicial.** Sale earliest 3 months after complaint. Sheriff posts and advertises in local newspaper for 3 weeks. The Indianapolis-area sheriff has standardized to [`indy.gov/activity/sheriff-real-estate-sales`](https://www.indy.gov/activity/sheriff-real-estate-sales).
2. **Tax sales.** **Two-stage tax-lien state**: Treasurer's tax sale (1-year redemption) → Commissioners' sale (120-day redemption) → tax deed.
3. **Top 3 counties.**
   - **Marion (Indianapolis)** — Assessor property cards at [`maps.indy.gov/AssessorPropertyCards/`](https://maps.indy.gov/AssessorPropertyCards/); recorder uses **Fidlar Direct Search** at [`inmarion.fidlar.com/INMarion/DirectSearch/`](https://inmarion.fidlar.com/INMarion/DirectSearch/); MapIndy GIS. **4/5**.
   - **Lake (Gary)** — XSoft Engage at [`engage.xsoftinc.com/lake`](https://engage.xsoftinc.com/lake); Public Access at [`in-lake.publicaccessnow.com`](https://in-lake.publicaccessnow.com/) (Tyler-style). **3/5** (XSoft is non-standard).
   - **Allen (Fort Wayne)** — Assessor COMPS + PATI; iMap at [`acimap.us`](https://www.acimap.us/). **3/5**.
4. **Code enforcement.** Indianapolis has open data ([data.indy.gov](https://data.indy.gov)); other cities limited.
5. **Probate.** Statewide **MyCase** courts portal — full coverage.
6. **Bulk parcel.** Excellent: **IndianaMap** at [`indianamap.org`](https://www.indianamap.org/) ships a statewide land-parcels file geodatabase pursuant to IC 6.1.1-4-25 (annual mandate). Plus [`gis-indianamap.opendata.arcgis.com`](https://gis-indianamap.opendata.arcgis.com/).
7. **Verdict — GREENLIGHT.** Statewide bulk parcels + statewide MyCase court portal cover most needs in two scrapers. Sheriff sales are county-fragmented but standardized within the Indy metro.

---

## MI — Michigan

1. **Foreclosure system.** **Foreclosure by advertisement (non-judicial)** is the dominant path. Notice published 4 weeks in a county newspaper, posted on property 15+ days pre-sale; sheriff's deed recorded with **Register of Deeds** within 20 days post-sale. Judicial foreclosure also exists.
2. **Tax sales.** **3-year forfeiture-foreclosure cycle** under MCL 211.78. Year 1 = delinquent, Year 2 = forfeited to county treasurer, Year 3 = foreclosed and auctioned. Wayne County's auction is the largest tax-foreclosure auction in the country ([`waynecountytreasurermi.com`](https://waynecountytreasurermi.com/)).
3. **Top 3 counties.**
   - **Wayne (Detroit)** — ROD at [`waynecountylandrecords.com`](https://www.waynecountylandrecords.com/) is **paywalled** ($6 account + $2/page); treasurer auction is open. Forfeited list published openly. **2/5** for ROD, **4/5** for treasurer.
   - **Oakland (Pontiac)** — ROD **Super Index** at [`ocmideeds.com`](https://www.ocmideeds.com/MIOakland/MVPSearch.WebSite/super-index) with PRN notification; delinquent-tax info at [oakgov.com](https://www.oakgov.com/government/oakland-county-treasurer-s-office/property-taxes/delinquent-property-taxes). **4/5**.
   - **Macomb (Mt. Clemens)** — Tyler-hosted self-service at [`macombcountymi-web.tylerhost.net/web/`](https://macombcountymi-web.tylerhost.net/web/). **4/5**.
4. **Code enforcement.** Detroit publishes **blight tickets** as open data — exceptional. Other cities limited.
5. **Probate.** State One Court of Justice portal; coverage uneven.
6. **Bulk parcel.** State portal at [`gis-michigan.opendata.arcgis.com`](https://gis-michigan.opendata.arcgis.com/) but no single bulk parcel file — counties self-publish (Wayne ships shapefile zips for free).
7. **Verdict — YELLOW.** Wayne ROD's pay-per-view fence is a real obstacle for the most important county. Tax-foreclosure data is strong; lis pendens equivalent (foreclosure-by-advertisement) is published in newspapers and recorded later. Build for tax foreclosure first.

---

## PA — Pennsylvania

1. **Foreclosure system.** **Judicial.** Lender files complaint in **Court of Common Pleas**; Prothonotary issues writ to sheriff after judgment. Sheriff sale advertised 3 weeks; up to 2 postponements within 130 days.
2. **Tax sales.** **3-stage tax-claim system** (Real Estate Tax Sale Law): Upset Sale (each Sept, sold subject to liens) → Judicial Sale (free & clear, court-petitioned) → Repository. Run by **County Tax Claim Bureau**.
3. **Top 3 counties.**
   - **Philadelphia** — **OPA Property** at [`property.phila.gov`](https://property.phila.gov/), **Atlas** at [`atlas.phila.gov`](https://atlas.phila.gov/) (best municipal map portal in the country), and **OpenDataPhilly** with the full assessment history dataset. **5/5**.
   - **Allegheny (Pittsburgh)** — Real Estate Portal at [`realestate.alleghenycounty.us/search`](https://realestate.alleghenycounty.us/search); County Property Viewer on Western PA Regional Data Center; Land Records via [`pa_allegheny.uslandrecords.com`](https://pa_allegheny.uslandrecords.com/). **5/5**.
   - **Montgomery / Bucks (Philly suburbs)** — Tax-claim-bureau pages list upset/judicial sale schedules; ROD records via county portals. **3/5**.
4. **Code enforcement.** Philadelphia L&I violations are **fully open** through OpenDataPhilly. Pittsburgh publishes 311/permit data. Suburban counties FOIA-only.
5. **Probate.** Register of Wills at the county level; Philadelphia and Allegheny have searchable indexes.
6. **Bulk parcel.** **PASDA** at [`pasda.psu.edu`](https://www.pasda.psu.edu/) is an excellent statewide clearinghouse, but the parcel layer is incomplete — coverage by county.
7. **Verdict — GREENLIGHT.** Philadelphia + Allegheny are top-3 metros in the entire study for data quality. Tax-sale 3-stage process is well-documented and predictable. Suburban PA is weaker but Philly+Allegheny carry the state.

---

## Recommended MVP Counties (validated)

The original system-prompt picks were Harris (TX), Cuyahoga (OH), Maricopa (AZ). After this research, **all three hold up**, but I'd **swap Maricopa for Philadelphia** for the MVP:

| Pick | Why it wins |
|---|---|
| **Harris County, TX** | HCAD ships bulk parcel; clerk on stable Tyler-style portal; non-judicial foreclosure means recorded notice precedes sale by 21 days (clean signal); largest investor market in the South. |
| **Cuyahoga County, OH** | Best municipal data stack in the country (MyPlace + GIS Hub + Cleveland open data); judicial foreclosure means sheriff-sale dockets are 100% court-of-record; high-volume tax-lien certificate sales. |
| **Philadelphia County, PA** *(swap from Maricopa)* | Atlas + OpenDataPhilly publishes parcel, OPA assessment, L&I code violations, deeds, and licenses all as queryable open data. One county = nearly every signal type, ideal for proving the architecture end-to-end. Maricopa is excellent but more single-purpose (tax-lien). |

If we want a **3rd state to be representative of the non-judicial west**, Maricopa is still the right backup pick — its Tax Lien Web platform is unique nationally.

---

## Synthesis

### State scoring table

| State | Foreclosure | Tax-sale | Online records (1-5) | Bulk data | Top scraper-friendly county | MVP priority |
|---|---|---|---|---|---|---|
| TX | Non-judicial | Tax-deed | 4 | Per-CAD bulk; no statewide | Harris | **HIGH** |
| FL | Judicial | Lien → deed | 4 | Statewide DOR roll | Miami-Dade | **HIGH** |
| OH | Judicial | Lien certificate | 5 | Per-county auditor hubs | Cuyahoga | **HIGH** |
| GA | Non-judicial | Redeemable deed | 3 | GIS1 (~20%) | Gwinnett | MED |
| NC | Power-of-sale w/ clerk | Judicial in-rem | 4 | NC OneMap statewide | Mecklenburg | **HIGH** |
| AZ | Non-judicial | Lien certificate | 4 | AZGeo aggregated | Maricopa | **HIGH** |
| TN | Non-judicial | Deed (1-yr redeem) | 3 | Comptroller per-county | Shelby | MED |
| IN | Judicial | Two-stage lien | 4 | IndianaMap statewide | Marion | MED-HIGH |
| MI | Foreclosure-by-ad | 3-year forfeiture | 3 | County-level only | Oakland (Wayne ROD paywalled) | MED |
| PA | Judicial | Upset/Judicial/Rep | 4 | PASDA partial | Philadelphia | **HIGH** |

### Key insight — the platform pattern

The single biggest finding is that **vendor consolidation is real and exploitable**:

- **Schneider Geospatial qPublic** is the assessor portal for the majority of Georgia counties and many in NC, FL, IN. **One qPublic scraper unlocks 30+ counties**.
- **Tyler Technologies** dominates clerk/court records (PublicSearch.us, Odyssey, Tyler Hosting). Dallas, Tarrant, Cuyahoga, Macomb all run Tyler. **One Tyler PublicSearch scraper unlocks ~50 counties** across TX/OH/MI alone.
- **Harris Govern Acclaim** (acclaim-web.*) covers Hamilton OH, Pinal AZ, and many others.
- **Fidlar Technologies** (DirectSearch) covers Marion IN and most of Indiana.
- **Esri ArcGIS REST endpoints** are the universal escape hatch — when a county publishes parcels as a Feature Service, we get JSON for free regardless of the front-end.

**Strategic implication:** instead of building "Harris County scraper", "Cuyahoga County scraper", etc., build **5 vendor-pattern scrapers** (qPublic, Tyler PublicSearch, Acclaim, Fidlar, ArcGIS-REST) plus a small per-county configuration layer. This collapses the build from "100 county integrations" to "5 vendor adapters + 100 config files" and is the right architecture for the MVP.

The two states that **don't fit this pattern** — Georgia (newspaper-only foreclosure notices) and Michigan (paywalled Wayne ROD) — are exactly the ones I marked Yellow.
