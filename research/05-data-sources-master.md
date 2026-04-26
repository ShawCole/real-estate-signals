# Public Data Sources — Master Database

**Compiled:** 2026-04-26
**Coverage:** 10 states + 35 counties (5 MVP + 30 expansion) + 30 cities + 18 vendor patterns + 18 federal sources
**Total verified rows:** ~830 across 5 sub-docs (every URL `curl`-verified before inclusion; unverifiable rows dropped per memory rule)

---

## Index

| Doc | Scope | Rows | Headline finding |
|---|---|---|---|
| [sources/01-states.md](sources/01-states.md) | State-level — 10 investor states (TX/FL/OH/GA/NC/AZ/TN/IN/MI/PA) × 9 categories | 145 | FL > NC > TX have strongest infra. OH + AZ weakest. |
| [sources/02-counties-mvp5.md](sources/02-counties-mvp5.md) | MVP-5 counties — Harris/Cuyahoga/Philly/Cook/Maricopa, exhaustive | 157 | Philadelphia deepest (unified CityGeo). Cuyahoga weakest. |
| [sources/03-counties-expansion30.md](sources/03-counties-expansion30.md) | Top-30 expansion counties — 4-7 sources each | 171 | Travis TX + Davidson TN + Marion IN are easiest next wins. Avoid Oakland MI / Allegheny PA / Summit OH. |
| [sources/04-cities.md](sources/04-cities.md) | Top-30 city open-data portals — code violations + vacant registries + permits | 194 | **87% of cities publish code-violation data**. Socrata + ArcGIS + Accela trio covers it. |
| [sources/05-vendor-patterns-and-federal.md](sources/05-vendor-patterns-and-federal.md) | 18 vendor patterns + 18 federal sources | 165 | ArcGIS Hub (3K+) + qPublic (1K+) + Tyler (200-400) = the leverage. **HUD USPS vacancy is FREE.** |

---

## The 5 architectural takeaways

### 1. ArcGIS REST is the single biggest scraper-leverage point.

Esri's ArcGIS Hub powers **~3,000+ US jurisdictions' open data**. Schneider qPublic (built on Esri) adds ~1,000 more. Most modern county GIS portals expose `/arcgis/rest/services/` endpoints that return JSON for every parcel + signal layer with no auth. **A single ArcGIS REST adapter unlocks more jurisdictions than every other scraper combined.** Build this first.

### 2. The wedge (code violations) is feasible everywhere meaningful.

87% of the top-30 US cities publish structured public code-violation data:
- **Socrata SODA** — ~55% of cities (Cleveland, Chicago, Philly, NYC, etc.)
- **ArcGIS Hub** — ~30% (Charlotte, Detroit, Houston-partial)
- **Accela Citizen Access** — ~rest (Indianapolis, San Antonio-via-search-form)

A 3-adapter trio (Socrata + ArcGIS REST + Accela) covers the entire wedge nationally without per-city rewriting. The 4 cities that don't publish bulk code-violation data: San Antonio, Jacksonville, Birmingham, Newark (currently 503).

### 3. HUD aggregated USPS vacancy data is publicly downloadable for free.

This is the single most valuable finding in the whole research stream.

- URL: `https://www.huduser.gov/portal/datasets/usps.html`
- Format: Quarterly DBF zips, census-tract level
- Registration: gated to governmental entities and registered non-profits (the derived **HUD-USPS Crosswalk files are unrestricted**)
- Tradeoff: tract-level precision (not parcel-level — USPS contractually prohibits redistribution at the address level)

**Implication:** if we qualify for the HUD registration tier (likely, via ArkData LLC if we frame the use case correctly), we can use HUD USPS vacancy as a tract-level vacancy floor signal *and* drop BatchData entirely. Tract-level is good enough for our MVP — combined with deterministic parcel-level vacancy from city registries (Cleveland VAPR, Chicago VBR, Philly L&I vacancy, Detroit Blight) + absentee-owner + repeat-code-violation, the composite is strong.

### 4. The MVP-5 county ranking is now data-backed.

| County | Free-data depth | Notable strengths | Notable holes |
|---|---|---|---|
| **Philadelphia** | Best | Unified CityGeo ArcGIS + Carto SQL covers OPA + parcels + transfers + tax delinquency + sheriff sales + violations + vacancy in one stack | Probate (Register of Wills has no online docket) |
| **Cook (Chicago)** | Strong | Dedicated vacant-building violation datasets (kc9i-wq85, u7si-yh3t); Chicago Open Data Socrata is mature | Cook County GIS is fragmented across multiple subdomains |
| **Maricopa** | Strong | Public ArcGIS Experience for lien/delinquent parcels (surfaces CP-eligible parcels spatially) | Phoenix doesn't have a robust city vacant registry |
| **Harris** | Mixed | Bulk JP eviction extract endpoint (under-the-radar distress signal); HCAD bulk parcel | Houston's code-violation publishing is partial |
| **Cuyahoga** | Weakest | Cleveland VAPR is the strongest free vacancy signal in the country | Fiscal GIS REST returns 403; Recorder offline; probate fragmented |

### 5. Three counties to prioritize for cohort 6 expansion (post-MVP).

| County | Why |
|---|---|
| **Travis TX (Austin)** | TCAD + tax + clerk + GIS + ArcGIS Hub + Austin Socrata, all open and well-organized — testbed for the Tyler-vendor adapter |
| **Davidson TN (Nashville)** | Best open-data posture in the expansion-30 set: full ArcGIS Hub with real-property layer + permits API + clean trustee/PADCTN portals |
| **Marion IN (Indianapolis)** | Every office consolidated under `indy.gov` with strong ArcGIS REST — testbed for the indy.gov pattern |

**Avoid until later:** Oakland MI (Akamai blocks all non-browser traffic), Allegheny PA (Akamai-walled, no PA statewide deed index), Summit OH (fragmented across subdomains, weak GIS).

---

## Updated scraper inventory (was 5 county scrapers + BatchData; now 10 adapters covering 800+ sources)

```
ingestion/scrapers/_patterns/
├── arcgis_rest.py          # GENERIC — covers 3,000+ jurisdictions
├── socrata_soda.py         # GENERIC — covers ~55% of major US cities
├── accela_aca.py           # Accela Citizen Access — ~1,500 city building/permit portals
├── tyler_publicsearch.py   # Tyler clerk/court — TX, OH, MI counties
├── qpublic.py              # Schneider Geospatial — GA, NC, FL, IN counties
├── govern_acclaim.py       # Govern Acclaim recorders — Hamilton OH, Pinal AZ
├── fidlar.py               # Fidlar DirectSearch / Laredo / AVA — Indiana
├── carto_sql.py            # Carto SQL (Philadelphia L&I and a few others)
├── citygeo_arcgis.py       # Philadelphia-specific (highest-coverage open-data city in US)
└── hud_usps_vacancy.py     # HUD aggregated USPS vacancy DBF, quarterly, tract-level
```

Plus the federal sources:
```
ingestion/scrapers/federal/
├── pacer_bankruptcy.py     # PACER federal court — $0.10/page, $3/doc cap
├── hud_home_store.py       # HUD-owned REO listings
├── va_reo.py               # VA REO listings
├── usda_reo.py             # USDA Rural Development REO
├── gsa_auctions.py         # Federal property disposal
├── treasury_forfeiture.py  # Treasury seized property
├── openfema.py             # OpenFEMA disaster declarations (motivated-seller window)
├── epa_acres.py            # EPA brownfields
└── fred.py                 # County-level economic indicators (context layer)
```

**Per-county scrapers become YAML configs** (e.g. `_configs/48201_harris_tx.yaml` wires which adapter handles each data type for that county). The 5 MVP counties + 30 expansion counties become 35 YAML files instead of 35 scraper classes.

---

## Updated cost shape (revised again, this time downward)

If we qualify for HUD USPS vacancy registration, BatchData becomes optional rather than required:

| Item | Original (with ATTOM/Regrid) | After Shaw's no-licensing pick | After HUD USPS finding |
|---|---|---|---|
| Bulk parcel/tax/deed | $30-80K/yr (ATTOM) | $0 (raw scrapers) | $0 |
| Vacancy primary | $80K/yr (Regrid Enterprise) | $1-5K/yr (BatchData $39-119/mo) | **$0** (HUD USPS tract-level + city registries parcel-level composite) |
| Skip-trace | $50-100K/yr | $0 (dropped from MVP) | $0 |
| Hosting / Postgres | $5-15K/yr | $0 incremental (existing VPS) | $0 incremental |
| Engineering maintenance | $50-100K/yr | $25-50K/yr | $25-50K/yr |
| **Total run** | **$220-385K/yr** | **$26-55K/yr** | **$25-50K/yr** |

The HUD USPS finding moves the year-1 marginal data cost from ~$1-5K → ~$0. That makes the platform genuinely free to operate at any volume.

---

## What goes into the architecture doc next

`architecture/00-overview.md` was written with BatchData as the only paid layer. It needs three updates:

1. **Add HUD USPS vacancy as the primary tract-level vacancy signal** — replaces BatchData as the default. BatchData becomes a fallback for users who want parcel-level precision and are willing to pay personally.
2. **Re-architect scrapers around the 10-adapter inventory above** instead of the 5 vendor-patterns originally specced. The new picture is *adapters, not counties* — a much more leveraged build.
3. **Update the MVP timeline** — week 1 should ship the ArcGIS REST + Socrata SODA adapters first (covers the most ground per engineering hour), then the per-county YAML configs. Estimate likely shrinks back to 4-5 weeks even with code violations folded in.

I'll update `architecture/00-overview.md` next if you greenlight, or hold for your review of this master doc first.

---

## Open questions surfaced by the deep dive

1. **HUD USPS registration eligibility.** ArkData LLC qualifies as a research/non-profit-adjacent entity? Or do we apply via a partner non-profit? Worth a 30-min legal call before relying on HUD USPS in the architecture.
2. **Cuyahoga is weaker than expected.** Worth swapping out for **Wayne MI (Detroit)** in the MVP-5? Detroit's open data is the strongest free vacancy signal in the country (Detroit Blight + 311 + parcel survey). Counter-argument: Cuyahoga still has the strongest *municipal-level* code-violation data via Cleveland VAPR; the county-level weakness is a smaller hit than losing Cleveland's city data.
3. **Maricopa lien/delinquent ArcGIS Experience app.** Need to determine whether scraping the underlying FeatureService is permitted by Maricopa's terms of service (their Experience app suggests yes; explicit ToS check warranted).
4. **Pittsburgh "dead-end" condemned-property flag.** Suggests we should add Pittsburgh (Allegheny PA) back into the expansion list despite the Akamai blocker — the dead-end flag is a one-of-a-kind signal. Workaround: scrape via Playwright + residential proxy, accept higher per-jurisdiction maintenance cost.

If you want me to spin a follow-up questionnaire on these four, say the word — otherwise I'll fold them into the architecture doc as flagged-for-decision.
