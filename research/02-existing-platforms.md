# Existing Distressed-Property Data Platforms — Competitive Analysis

*Phase 1.2 research. Last updated: April 2026. Investor-engineer audience. Pricing verified via vendor pages and 3rd-party listings; revisit quarterly because the per-month tiers churn constantly.*

---

## 1. PropStream

**What it does.** The default tool for U.S. SFR investors and wholesalers. Combines nationwide property records with 120+ filters, prebuilt distress lists (pre-foreclosure, tax-delinquent, vacant, absentee, high-equity), comp pulling, mailing-list export, and a "driving for dollars" mobile app. Skip-trace, list automation, and direct mail are paid add-ons.

**Likely data sources.** Self-reported as multi-sourced from "public records, MLS feeds, and private aggregators" — ~308M deeds, ~185M mortgages, ~70M MLS records. Strongly suspected to license from ATTOM and CoreLogic as backbone parcel/tax data, layered with their own MLS aggregation (PropStream sits inside Stewart Title's data org, which is a CoreLogic peer). They do not disclose vendors. Coverage is best in urban/suburban counties where records are digitized.

**Pricing (April 2026).** Essentials $99/mo (50 free skip-traces), Pro $199/mo (skip-trace included at higher volume), Elite custom. List Automator add-on ~$27/mo. Skip-trace pay-as-you-go ~$0.12–0.15/record. 10,000 exports/mo on base plan.

**Coverage / gaps.** Strong: SFR, owner-occupied/absentee, tax-delinquency, pre-foreclosure NODs, basic vacancy. Weak: commercial, code violations (not a native dataset), probate (limited), rural counties, anything requiring real-time county updates (refresh cadence is multi-week in many counties). MLS tab was removed at some point — users complained.

**Tech stack signals.** Public-facing API exists for partner integrations (e.g., a "Push to BatchDialer" connector is documented as a native API). No public developer portal, no published webhook spec, no rate-limit docs — this is a closed enterprise API granted to partners, not a self-serve dev product.

**User complaints.** From BiggerPockets and Trustpilot: (1) skip-trace results inferior to dedicated providers ("don't use it for skip tracing"), (2) feature removals (MLS tab), (3) postcard direct-mail service has refund/delivery issues, (4) data quality dips in rural areas, (5) cost stack — base $99 inflates fast once you add skip-trace + List Automator + mail.

**What we'd build differently.** Real-time county ingest with timestamps surfaced in the UI (so users can sort by "filed in last 24 hrs"). Native code-violation and municipal-lien layer that PropStream doesn't carry. API-first, not API-as-afterthought.

Sources: [PropStream Pricing](https://www.propstream.com/pricing), [DealRun pricing breakdown](https://dealrun.ai/blog/propstream-pricing-breakdown), [PropStream public records source piece](https://www.propstream.com/news/how-propstream-can-help-you-sort-out-public-records-and-mls-data), [BiggerPockets — why PropStream sucks](https://www.biggerpockets.com/forums/898/topics/1032178-why-propstream-sucks), [Trustpilot](https://www.trustpilot.com/review/propstream.com), [Push-to-BatchDialer API](https://www.propstream.com/news/push-to-batchdialer-seamless-api-sync-from-propstream-to-batchdialer-for-high-volume-calling).

---

## 2. BatchLeads / BatchSkipTracing (BatchData)

**What it does.** List-pulling + skip-tracing combo, marketed as the cheaper PropStream-killer. Ships with a built-in dialer (BatchDialer), AI deal-analysis assistant (Reia AI), list stacking, and a sister product BatchData that exposes the underlying property + skip-trace API to developers. BatchSkipTracing was rebranded to BatchData; the BatchLeads consumer UI sits on top of the same data.

**Likely data sources.** Self-reported as ~155M properties across 3,100+ counties via "more than a dozen data sources" plus proprietary algorithms. Almost certainly aggregates ATTOM and CoreLogic underneath, plus their own skip-trace vendor stack (TLO/LexisNexis-tier). Skip-trace identity graph is a real differentiator — they invest there.

**Pricing (April 2026).** BatchLeads tiers: Personal Basic $39/mo (250 skip-trace credits included), Personal Plus $59/mo (1,000 credits), Growth $119/mo, Enterprise custom. Standalone BatchSkipTracing/BatchData: $0.20 per skip-trace result (no monthly minimum, free signup). Promo codes (START80, TITANIUM) routinely cut first-month cost 50–80%.

**Coverage / gaps.** Strong: skip-tracing accuracy at price point, list-stacking UX, real API + webhooks (the *only* one in the consumer-investor tier with real dev tooling). Weak: MLS data is stale in user reports (properties marked pending that sold a year ago), customer service, no-refund policies. Not a serious commercial player.

**Tech stack signals.** Real developer portal at batchdata.io/api-solutions. Webhooks documented for new-listing alerts and property enrichment. Integrates with Twilio, Plivo, Telnyx, Podio, Zapier, SignalWire, Flowroute. They are the closest competitor to "API-first" in this space.

**User complaints.** Outdated MLS, inaccurate skip-trace results (lots of disconnected numbers), bug-heavy app, dialer reliability, strict no-refund policy, "wallet" credit lockup. Customer support gets mixed reviews — praised in some threads, slammed in others.

**What we'd build differently.** Keep the API-first posture (this is the right move) but ship freshness SLAs per data type and expose source-of-record + last-pulled timestamp on every record. The "is this lead actually current?" question is unanswerable in BatchLeads today.

Sources: [BatchLeads pricing](https://batchleads.io/pricing), [BatchSkipTracing pricing G2](https://www.g2.com/products/batchskiptracing/pricing), [BatchData API](https://batchdata.io/api-solutions), [BatchSkipTracing review](https://www.realestateskills.com/blog/batchskiptracing-review), [BiggerPockets BatchLeads thread](https://www.biggerpockets.com/forums/93/topics/862726-recommend-batchleadsio-or-something-better), [Trustpilot](https://www.trustpilot.com/review/batchleads.io).

---

## 3. DealMachine

**What it does.** Mobile-first "driving for dollars" app — drive a neighborhood, tap properties, get owner info instantly, send direct mail from your phone. Has expanded into desktop list-pulling and an AI dialer, but the GPS/route-tracking driving workflow is the moat. Pivoted in 2024–2025 to bundle "unlimited" skip-trace into all plans.

**Likely data sources.** Their skip-trace product is built on the 911 directory database that powers ~90% of caller IDs in the U.S. (per their own marketing) — strongly implies a relationship with a CNAM provider (Neustar/TransUnion or similar). Property data is a multi-source mix; presence of MLS data implies an MLS aggregator deal. Likely ATTOM under the hood for parcel/tax.

**Pricing (April 2026).** Starter $99/mo (1 user, 20K leads, unlimited skip-trace), Pro $149/mo (3 users, 60K leads, route planning), Pro Plus $232/mo (6 users, 120K leads). Annual billing -17%. Direct-mail pieces $0.57–$0.67 each. Calling minutes, AI voicemail drops billed à la carte.

**Coverage / gaps.** Strong: mobile UX is genuinely best-in-class, route planning, the social/team features for D4D crews, unlimited skip-trace. Weak: desktop experience is a port (not native-feeling), email skip-trace accuracy ~10% per user reports, no phone support, scope is residential-only.

**Tech stack signals.** No public API or developer docs. Mobile-first stack (iOS/Android native), minimal third-party integrations beyond direct mail vendors and CRM exports. Closed ecosystem.

**User complaints.** Email skip-trace accuracy poor (~10%), phone support absent, direct-mail spend balloons fast on aggressive campaigns, ongoing debate on Reddit about whether the "unlimited skip trace" is the same data quality as paid per-lookup providers (it isn't).

**What we'd build differently.** D4D doesn't scale — the wedge is finding distressed properties *before* anyone needs to drive past them. Use computer vision on Street View / Mapillary / drone imagery + permit data + utility-shutoff signals to surface candidates that DealMachine users would have driven past. Keep mobile, but for *verification*, not discovery.

Sources: [DealMachine Pricing](https://www.dealmachine.com/pricing), [DealMachine Skip Tracing](https://www.dealmachine.com/skip-tracing), [Real Estate Skills review](https://www.realestateskills.com/blog/dealmachine-review), [BiggerPockets free skip-trace thread](https://www.biggerpockets.com/forums/80/topics/1173240-deal-machine-free-skip-tracing-any-good).

---

## 4. Privy

**What it does.** MLS-comp-focused investor tool. Differentiator: pulls direct first-party MLS data (the kind reserved for licensed agents) and runs a proprietary investor-comp algorithm called LiveCMA that scores deals based on prior closed flips. Strong on investor-activity tracking ("who bought what, when, for how much"). Light on distress signals.

**Likely data sources.** Direct MLS feeds in ~97% of MLS coverage areas (refreshed every ~15 min), plus public-record overlays for liens, auctions, tax assessments, deeds. MLS access is paid via per-MLS data licensing (MLSes charge real money for IDX/RETS feeds) — this is their cost moat *and* their wedge. Public-records side likely sourced from ATTOM/CoreLogic.

**Pricing (April 2026).** Investor plan starts $149/mo (1 state). Multi-state and national tiers run higher; varies by market. Annual billing saves 20%. Some sources cite a $99/mo entry, but the official pricing page anchors at $149.

**Coverage / gaps.** Strong: real-time on-market deals, comping speed, non-disclosure-state coverage (Texas etc.) where public records can't tell you sold price, investor activity heatmaps. Weak: pre-foreclosure surfacing, code violations, probate, vacancy — they admit publicly that off-market distress is overlay data, not core. Not a wholesaling tool.

**Tech stack signals.** No public API. Web app + mobile. Their MLS licensing posture suggests deep RETS/Spark/Trestle integrations on the back end but they don't expose it.

**User complaints.** Aggressive billing/auto-renewal (Trustpilot 2.3/5 average is largely billing complaints), some local listings missing from coverage, expensive at the multi-state tier, customer service slow.

**What we'd build differently.** Their MLS-direct posture is the right model for *on-market* freshness. We don't compete here — we layer the distress signals they ignore (code-violation ingest, probate court scraping, utility shutoff) and feed *into* their workflow via a deal-feed API.

Sources: [Privy pricing](https://www.privy.pro/pricing/), [Privy product](https://www.privy.pro/product/), [Real Estate Skills review](https://www.realestateskills.com/blog/privy), [Capterra reviews](https://www.capterra.com/p/173835/Privy/).

---

## 5. REIPro

**What it does.** All-in-one for the small/mid wholesaler: property data + lead lists + investor CRM + direct-mail templates + deal analyzer + contract generation + skip-trace, all under one subscription. Targets the "I don't want to wire 5 tools together" market.

**Likely data sources.** Doesn't disclose. Public-records-driven property database (millions of records), with built-in skip-trace at credit-based pricing. Strongly suspect ATTOM or a cheaper aggregator (DataTree/First American is plausible) underneath, plus a TLO-tier skip-trace vendor.

**Pricing (April 2026).** Plans range from $57/mo (single-user entry) to ~$197/mo for fuller feature access. Some sources peg the headline plan at $97/mo. 14-day free trial. Skip-trace at ~$0.13/credit (1 credit basic / 2 premium).

**Coverage / gaps.** Strong: workflow consolidation, contract automation, follow-up sequences, motivated-seller lead lists, beginner-friendly UX. Weak: data freshness, API depth (only Zapier-mediated webhooks via a public Zapier integration), skip-trace accuracy (users report disconnected numbers), brand visibility versus PropStream/BatchLeads.

**Tech stack signals.** Zapier integration with webhook support. No native developer API or docs surfaced publicly. Looks like a workflow-product layer over licensed data — not a data company.

**User complaints.** Skip-trace data quality ("disconnected numbers, wrong people"), interface dated, support adequate but not great.

**What we'd build differently.** They proved the bundled-suite thesis works for wholesalers but they're stuck with stale data because they don't own the pipeline. We can win this segment by being the data + signal layer that powers *their* CRM via API rather than competing on UX.

Sources: [REIPro review](https://www.realestateskills.com/blog/reipro-review), [REIPro skip-trace docs](https://support.myreipro.com/hc/en-us/articles/4406964536987-How-to-Skip-Trace-Overview), [REIPro Zapier webhooks](https://zapier.com/apps/reipro/integrations/webhook).

---

## 6. ATTOM Data

**What it does.** B2B bulk property data + API supplier. Powers a large chunk of the consumer-facing investor tools above. Sells via API, bulk data licensing (host on your own servers), cloud-delivery (Snowflake/Databricks/GCP/AWS marketplaces), and Match & Append services. Also runs a thin consumer product called Property Navigator. Not aimed at retail investors.

**Likely data sources.** Aggregates from 3,000+ U.S. counties directly (assessor, recorder, court). Layers in their own AVM, environmental risk, neighborhood, geospatial. ~158M properties, ~30B transactional rows, 7,200+ attributes. They are an upstream source, not a consumer of others.

**Pricing (April 2026).** API entry at ~$95/mo for limited access. Real enterprise deployments routinely start at $500/mo and scale into 5-figure monthly contracts. Bulk licensing is custom-quoted, typically $10K–$100K+/yr depending on geography and refresh cadence. Not transparent — sales-led.

**Coverage / gaps.** Strong: nationwide tax/deed/mortgage/foreclosure depth, AVM, environmental/risk overlays, refresh discipline. Weak: code violations and municipal liens (these are city-level and ATTOM doesn't ingest most cities' building/permit data), probate (some counties only), MLS (they have AVMs but not real listing feeds). Hostile pricing for sub-enterprise customers.

**Tech stack signals.** Mature REST API with a real developer portal at api.developer.attomdata.com, well-documented endpoints, Postman collections published. Bulk data via SFTP or cloud delivery. This is the most professional dev experience in the space.

**User complaints.** Cost. Sales-cycle friction. Many would-be small users get priced out. Data accuracy is generally trusted; the gripes are commercial.

**What we'd build differently.** ATTOM is a *supplier*, not a competitor. The right move is to license ATTOM (or a competitor like DataTree) as our backbone and add the layers ATTOM ignores: code violations, probate scraping, utility data, vacancy via deliverability/USPS NCOA. Then re-package as a developer-first API with transparent per-call pricing — undercut ATTOM on price for the long-tail dev market they don't serve.

Sources: [ATTOM data page](https://www.attomdata.com/data/), [ATTOM API docs](https://api.developer.attomdata.com/docs), [ATTOM bulk licensing](https://www.attomdata.com/solutions/bulk-data-licensing/), [Datarade ATTOM profile](https://datarade.ai/data-providers/attom/profile).

---

## 7. CoreLogic (now Cotality)

**What it does.** The institutional bedrock of U.S. real estate data — feeds banks, insurers, GSEs (Fannie/Freddie), title companies, and most of the AVMs in production. Rebranded to Cotality in 2024–2025. Sells custom APIs, bulk data, and cloud-marketplace integrations (Snowflake/Databricks). Owns ListSource. ~50 years of data, 5.5B records, 99.9% U.S. housing market coverage.

**Likely data sources.** They *are* the source for most of the market. County records direct, MLS aggregation, their own AVM (one of the two industry-standard AVMs alongside HouseCanary), proprietary insurance/risk data, mortgage performance data, HPI (home price index) over 25 years. They acquire data companies regularly.

**Pricing (April 2026).** Not published. Custom enterprise contracts only. Real-world floor is in the $5K–$50K/mo range for meaningful API access; bulk licensing deals can run into seven figures.

**Coverage / gaps.** Strong: everything residential at scale, including pre-foreclosure NOD/NOS data, tax-delinquency, mortgage performance, AVM, flood/hazard, ownership chain. Weak from a *startup competitor's* view: zero accessibility for small developers, no transparent pricing, no self-serve, contracts are legalese-heavy, and they don't carry the granular city-level signals (code violations, permits, utility shutoffs) that drive distress at the property level.

**Tech stack signals.** Mature APIs but gated by sales. Cloud delivery via marketplaces. Snowflake/Databricks integrations. Enterprise-grade everything; nothing self-serve.

**User complaints.** Largely from competitors and journalists, not end users — pricing opacity, vendor lock-in, slow product velocity. End users are insurance carriers and banks who tolerate it because there is no real alternative at their scale.

**What we'd build differently.** Don't compete with CoreLogic head-on. Be the API-first, transparently-priced, signal-layer alternative for the segment they ignore: developers and small teams building products with property data. Use the same county-record pipelines they use, augmented with city-level signals they skip.

Sources: [Cotality/CoreLogic profile](https://datarade.ai/data-providers/corelogic/profile), [HomeSage 2026 data providers ranking](https://homesage.ai/resources/blog/best-real-estate-data-providers-2026/), [APIscrapy comparison](https://apiscrapy.com/comparing-top-5-real-estate-data-providers-usa/).

---

## 8. Reonomy (Altus Group)

**What it does.** Commercial real estate (CRE) intelligence — the "PropStream for commercial." Owners, debt, tenants, transactions, comps for office/industrial/multifamily/retail. Acquired by Altus Group in November 2021 for $201.5M; now sold under the Altus umbrella. Targets brokers, developers, lenders, investors who need to source off-market commercial deals and contact decision-makers.

**Likely data sources.** Public records (assessor, transaction, deeds) at the commercial parcel level + proprietary entity-resolution (the hard part: mapping LLCs and shell entities back to actual decision-makers). Census/geospatial overlays. Tenant data via web scraping and their own surveys. ML-based ownership graph is the moat.

**Pricing (April 2026).** Not public. Sales-quoted. 7-day free trial available with full feature access. Real-world contracts widely reported in $200–$500/mo for individual users and $1K–$5K+/mo for teams. Per-seat pricing is rigid (additional payment for assistants).

**Coverage / gaps.** Strong: CRE entity resolution, owner-LLC unmasking, contact info for decision-makers in major metros (NYC tri-state, Chicago, FL, CA), debt maturity tracking. Weak: rural and secondary markets, contact data accuracy in those markets, billing practices (many Trustpilot complaints about post-cancellation charges), no SFR coverage.

**Tech stack signals.** Has an API (mentioned on Altus product pages) but it's gated and enterprise-priced. Web app + integrations primarily.

**User complaints.** Auto-renewal/cancellation charges (recurring Trustpilot theme — "mafia-esque"), data accuracy in non-major-metro markets, per-seat pricing inflexibility, support quality declining post-Altus acquisition.

**What we'd build differently.** Reonomy proved the entity-resolution model has real value in CRE. We're not focused on CRE for v1, but the same LLC-unmasking technique applies to SFR investor portfolios and would be a useful side feature. If we expand into CRE later, the entry is honest pricing + better rural coverage + no auto-renewal traps.

Sources: [Altus/Reonomy](https://www.altusgroup.com/solutions/reonomy/), [CRE Daily Reonomy review](https://www.credaily.com/reviews/reonomy-review/), [Altus acquisition press](https://www.altusgroup.com/press-releases/altus-group-acquires-reonomy/), [Capterra Reonomy reviews](https://www.capterra.com/p/208413/Reonomy/reviews/), [Trustpilot](https://www.trustpilot.com/review/www.reonomy.com).

---

## 9. ListSource (CoreLogic)

**What it does.** Pure direct-mail list builder — the "old school" tool agents and wholesalers used to pull targeted homeowner lists for postcard campaigns before SaaS dashboards existed. Owned by CoreLogic and powered by their data backbone. Pay-per-record, not subscription-first.

**Likely data sources.** CoreLogic's full backbone — assessor, deeds, mortgage, owner demographics, parcel maps. ~134M property records, ~94% U.S. coverage.

**Pricing (April 2026).** Per-record: roughly $0.15–$0.31/record depending on filter complexity. Order minimums ~$50–$75. Three plan structures: Subscription (committed direct-mail buyers, 99% coverage, free phone support), Build & Buy (no contract, pay-as-you-go), License & Resell (for service providers).

**Coverage / gaps.** Strong: filter depth, data accuracy (CoreLogic), one-shot list pulls without subscription. Weak: no app/dashboard for ongoing monitoring, no skip-trace included, no mail send (just the list), UI is dated, not built for high-frequency users.

**Tech stack signals.** Web-based list builder. Some bulk export/CSV. No public API for ListSource itself (you'd go upstream to CoreLogic).

**User complaints.** Pay-per-record gets expensive fast for testing, minimum order requirements hurt small budgets, no monitoring or alerts, dated UX. But data quality is rarely complained about — the CoreLogic backbone is trusted.

**What we'd build differently.** ListSource is a one-shot list pull — we're a continuous signal stream. The upgrade path is "subscribe to a query, get notifications when new properties match" — what the modern user expects. Same data foundation, different consumption model.

Sources: [ListSource pricing](https://www.listsource.com/pricing/index.htm), [ListSource review (Real Estate Skills)](https://www.realestateskills.com/blog/listsource-review), [Hooquest ListSource](https://hooquest.com/lists/listsource/).

---

## 10. Foreclosure.com

**What it does.** Subscription consumer site for foreclosure listings — pre-foreclosures, REOs, short sales, bank-owned, HUD/Fannie/Freddie inventory across all 50 states. Aimed at consumer-investors and homebuyers, not wholesalers. Has a mobile app and email alerts.

**Likely data sources.** Self-disclosed: courthouse records, Fannie Mae, Freddie Mac, HUD, "and more." Heavy reliance on courthouse scraping for NOD/NOS notices. Likely augmented by REO listings sourced from servicer feeds or scraped from servicer websites.

**Pricing (April 2026).** $39.80/mo after a 7-day free trial. Annual options exist. Cheap relative to PropStream.

**Coverage / gaps.** Strong: nationwide foreclosure breadth, low price, beginner-friendly. Weak: data freshness (recurring complaint that listings stay up after sale), no skip-trace, no comping, no list export, no API, no investor-grade filtering. It's a consumer search portal, not an investor pipeline.

**Tech stack signals.** Web + mobile app. No API. No developer presence.

**User complaints.** Trustpilot (53 reviews, mixed) — billing/auto-renewal abuse ("free trial" charging immediately), stale listings (3-year-old data appearing as fresh), confusing cancellation. Some positive reviews from users who genuinely closed deals via the email alerts.

**What we'd build differently.** Foreclosure.com is yesterday's tool. Real-time courthouse scraping with timestamps + automatic delisting when foreclosures cure or properties sell + investor-grade filters and exports + transparent billing. Same niche, modern execution.

Sources: [Foreclosure.com FAQ](https://www.foreclosure.com/questions.html), [Real Estate Skills review](https://www.realestateskills.com/blog/foreclosure-com-review), [Trustpilot](https://www.trustpilot.com/review/foreclosure.com).

---

## 11. RealtyTrac

**What it does.** Historical foreclosure aggregator. The granddaddy of distressed-property data — drove a lot of post-2008 investor activity. ATTOM owned it for years; in July 2022 ATTOM sold RealtyTrac (and Homefacts) to Nations Info, which became an ATTOM data licensing customer. So today it's a downstream consumer brand, not a data company.

**Likely data sources.** ATTOM is the underlying data licensor (per the 2022 sale terms, Nations Info licenses ATTOM data to power RealtyTrac). So coverage matches ATTOM's foreclosure footprint: NODs, Lis Pendens, NTS, NFS, REOs across 3,000+ counties.

**Pricing (April 2026).** Subscription model, current pricing not transparently published — historically in the $50–$70/mo range. Free tier with limited views. Has not kept pace with PropStream-class tools on UX.

**Coverage / gaps.** Strong: historical foreclosure data depth (decades), brand recognition, ATTOM-grade national foreclosure coverage. Weak: investor-grade filtering, list-pulling and export workflows, skip-tracing, modern UX. Most serious investors moved off RealtyTrac years ago to PropStream/BatchLeads.

**Tech stack signals.** Consumer site. No developer API. Operates as a content site monetized by subscriptions.

**User complaints.** Diminished relevance, dated UX, subscription friction. Less commentary than peer tools because the active investor user base shrunk.

**What we'd build differently.** RealtyTrac's brand and historical depth could be a moat if the product caught up — but it hasn't. The lesson: foreclosure data alone is no longer a sufficient product. You need code violations, probate, vacancy, and skip-trace stacked on top. We treat foreclosure as one signal among many, not the headliner.

Sources: [ATTOM sells RealtyTrac to Nations Info (PRNewswire 2022)](https://www.prnewswire.com/news-releases/attom-sells-realtytrac-to-nations-info-301595039.html), [HelloData RealtyTrac explainer](https://www.hellodata.ai/help-articles/what-is-realtytrac), [ATTOM foreclosure data](https://www.attomdata.com/data/foreclosure-data/).

---

## 12. Honorable Mentions

### PropertyRadar
California-born platform, now nationwide. Court-sourced divorce/probate/eviction data is unusually deep — they ingest court records directly. Pricing $49/mo (Essential) to $79/mo (Complete) — cheaper than PropStream. Version 5.0 (March 2026) added multi-channel marketing and AI capabilities. Users report phone-number accuracy *better* than PropStream. Best for CA investors and anyone who needs probate/divorce signals natively. Weak outside major MSAs in some categories. [PropertyRadar 5.0 launch](https://markets.financialcontent.com/stocks/article/bizwire-2026-3-17-propertyradar-launches-version-50-with-expanded-multi-channel-marketing-court-sourced-data-and-ai-capabilities), [BiggerPockets thread](https://www.biggerpockets.com/forums/93/topics/1081306-anyone-here-using-propertyradar).

### DataTree (First American)
Title-industry data platform. Massive recorded-document image archive, FlexSearch full-text search across recorded docs (find any name across millions of documents), AVMs, ownership/encumbrance reports. $150–$275/mo. Used heavily by title professionals; investors mostly don't know about it. Could be a backbone supplier for us if ATTOM gets too expensive. [DataTree on GetApp](https://www.getapp.com/marketing-software/a/datatree/), [First American 2025 retro](https://dna.firstam.com/insights-blog/search-reinvented-how-datatree-evolved-in-2025-and-what-it-means-for-2026).

### REsimpli
AI-CRM-first competitor to PropStream. $149–$599/mo. Heavy on AI agents (VoiceFollow, CallAnswer, CallGrade, SpeedToLead) for follow-up automation. Not a primary data source — data layer is licensed. They are running active anti-PropStream marketing in 2026. Useful as a benchmark for "what does the modern CRM-first wholesaler stack look like." [REsimpli](https://resimpli.com/), [2026 anti-PropStream campaign](https://finance.yahoo.com/news/resimpli-launches-2026-campaign-highlighting-161400618.html).

### Zillow "Make Me Move" / off-market signals
Zillow killed the public Make Me Move feature years ago. There is no public Zillow API for property data in 2026 — it's a confusing mix of restricted partner access, scraper APIs, and unified third-party services. Off-market status on Zillow is just a sync-lag indicator, not an investor signal. Useful only as an MLS-status reference; not a real distress data source. [RealtyAPI 2026 guide](https://www.realtyapi.io/blog/how-to-get-property-data-from-zillow-using-an-api-2026-guide), [Zillow Group developers](https://www.zillowgroup.com/developers/).

### REIReply
Communications layer (SMS/voice AI) at $99/mo, not a data platform. AI assistant ("REBA") cold-calls and qualifies leads; contract automation and direct-mail bundled. Useful as a downstream destination for our signal feeds — we should plan to integrate. [REIReply](https://reireply.com/).

---

## Pricing Landscape Table

| Platform | Entry tier $/mo | Pro tier $/mo | Skip-trace $/lookup | Public API? |
|---|---|---|---|---|
| PropStream | $99 | $199 | $0.12–0.15 | Partner only |
| BatchLeads / BatchData | $39 | $119 | $0.20 (or included) | **Yes (real)** |
| DealMachine | $99 | $232 | "Unlimited" included | No |
| Privy | $149 | varies by mkt | n/a (not core) | No |
| REIPro | $57–$97 | $197 | $0.13 | Zapier only |
| ATTOM Data | $95 (limited) | $500+ enterprise | n/a | **Yes (mature)** |
| CoreLogic / Cotality | Custom ($5K+/mo realistic) | Custom | n/a | Sales-gated |
| Reonomy | ~$200+ | $1K+ teams | n/a | Sales-gated |
| ListSource | $0 (per-record) | per-record | n/a | No |
| Foreclosure.com | $39.80 | — | n/a | No |
| RealtyTrac | ~$50–$70 | — | n/a | No |
| PropertyRadar | $49 | $79 | bundled | No |
| DataTree | $150 | $275 | n/a | **Yes** |
| REsimpli | $149 | $599 | bundled | No |

---

## Coverage Matrix

Legend: ✓ first-class, ~ partial/weak, ✗ not offered

| Platform | Tax-delinq | Pre-fcl | Code viol | Probate | Vacancy | Skip-trace | Comps | MLS-direct |
|---|---|---|---|---|---|---|---|---|
| PropStream | ✓ | ✓ | ✗ | ~ | ~ | ~ | ✓ | ~ |
| BatchLeads | ✓ | ✓ | ✗ | ~ | ✓ | ✓ | ✓ | ~ (stale) |
| DealMachine | ✓ | ✓ | ✗ | ~ | ✓ | ✓ | ✓ | ~ |
| Privy | ~ | ~ | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| REIPro | ✓ | ✓ | ✗ | ~ | ~ | ~ | ✓ | ~ |
| ATTOM | ✓ | ✓ | ✗ | ~ | ~ | ✗ | ✓ | ~ |
| CoreLogic | ✓ | ✓ | ✗ | ~ | ~ | ✗ | ✓ | ✓ |
| Reonomy | ~ | ~ | ✗ | ✗ | ✗ | ~ (CRE) | ✓ (CRE) | ✗ |
| ListSource | ✓ | ✓ | ✗ | ~ | ~ | ✗ | ✗ | ✗ |
| Foreclosure.com | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ |
| RealtyTrac | ✓ | ✓ | ✗ | ~ | ~ | ✗ | ~ | ✗ |
| PropertyRadar | ✓ | ✓ | ~ | **✓** | ~ | ✓ | ✓ | ~ |
| DataTree | ✓ | ✓ | ✗ | ~ | ~ | ~ | ✓ | ~ |

**Pattern.** Code violations are an industry-wide gap (PropertyRadar is the only one with even partial coverage). Probate is patchy everywhere except PropertyRadar. Vacancy is shallow nearly everywhere — usually inferred from USPS NCOA or last-mailed-return data, not direct utility/occupancy signals. MLS-direct is dominated by Privy and CoreLogic with no real challenger at the SMB price point.

---

## Top 5 Gaps in the Market — Where Our Wedge Lives

1. **Code violations and municipal liens.** No major platform ingests city building-department feeds at scale. PropertyRadar has a limited version. This is genuinely hard (every city's a different dataset) but the city-level ingest pipeline is a defensible moat once built — and it surfaces *the* cleanest distress signal that exists (a property cited for unsafe-structure or hoarding violations is *vastly* more likely to sell to a wholesaler than a property merely behind on taxes). **Top wedge.**

2. **Real-time freshness with provenance.** Every platform refreshes on a schedule (weekly, monthly, quarterly per data type) but none expose source-of-record + last-updated timestamps to end users. Investors cannot answer "was this NOD filed yesterday or 90 days ago?" without manually checking the courthouse. A platform that surfaces freshness as a first-class UI element and ingests in near-real-time wins on trust alone.

3. **API-first developer experience for the SMB tier.** ATTOM and CoreLogic have APIs but are sales-gated and expensive. BatchData has the only real self-serve API in the consumer-investor space and they monetize it well. There is room for a *better* dev product — webhooks for new-distress events, transparent per-call pricing, free dev tier, modern OpenAPI docs, SDKs in TypeScript/Python/Go. This is also how we sell to PropTech startups, real-estate fintech, and CRMs (REsimpli, REIPro, REIReply) who would rather buy data than build it.

4. **Probate and life-event signals at scale.** PropertyRadar pulls court records for divorce/probate/eviction in their footprint, but coverage is uneven. The data is hard because probate is filed in a separate court system in most counties (probate court vs. recorder of deeds), and many smaller counties don't publish probate filings online. A national probate ingest pipeline (county-by-county scraping + obituary-cross-match) would be a unique offering — and probate leads are some of the highest-conversion in the wholesaling industry.

5. **Vacancy detection beyond USPS NCOA.** Everyone uses USPS NCOA "vacant" flags, which are a lagging proxy. The next-gen signals are: utility shutoffs (electric/water — available in some cities), Street View / aerial imagery analysis (overgrown lawns, boarded windows, mail piles), short-term-rental delistings (Airbnb removal can signal distress), and code-violation registrations of vacant properties. A platform that combines 3+ of these into a confidence-scored vacancy signal would dramatically out-perform incumbents.

---

*Compiled April 24, 2026. All pricing and feature claims verified against vendor pages or 3rd-party listings on the date written; revisit quarterly.*
