# Research Plan

The plan for "Next up" step 3 (`CLAUDE.md`). Set up in devlog 019. **Complete (devlogs 020–028).** Passes A–D, source check, folded into `inference-map.md` and built into `index.html`.

---

## 1. Purpose

**Discovery first.** Find out what can be inferred about a person from the data this page collects, by searching for correlations others have documented. Some of those will become inferences we haven't thought of yet. Checking the rules we already have is a secondary track (§7).

**The bar for a finding** (from the user's guiding principles, `inference-map.md` → Standards):
- It's about *this person*, not their area or people in general
- It reveals something hidden: the "how did it know that?" moment
- The user's examples (income band and rent from hardware plus location; immigration timing from a phone's model and age; employer from the network; likely industry) show the *type* of inference wanted. They are not a checklist to verify

**Three perspectives worth reflecting back:**
- What research shows (correlations with evidence behind them)
- How advertisers, marketers and data brokers characterize people like the visitor, and how they'd target them
- How a bad actor could use the same data (scams, discrimination, stalking, persecution)

**Accuracy isn't required for industry inferences.** If advertisers, marketers or bad actors draw an inference, that is the finding, even when their data is unreliable. Sometimes the weak basis is itself the point: "advertisers label you X based on nothing more than Y."

## 2. Scope

- **US only** (sources, populations and datasets)
- **All data the page collects**, grouped as in the engine (§5)
- **New data points** the current setup could collect, where a source shows they'd unlock a meaningful inference. They must be local, or pass the third-party rule (`CLAUDE.md` → Project rules)
- **Sensitive topics are in scope for the search:** gender, sexuality, health, disability, religion, politics, immigration, finances, age (incl. minors). Each finding is marked ⚑ and decided case by case before anything is built

## 3. Where to look

Cast a wide net. Reliability can be traded off when the wording carries the source's perspective (§4).

- **Peer-reviewed research:** economics (e.g. device ownership and income), credit scoring from digital footprints, browser fingerprinting and security studies, human-computer interaction, sociology and demography
- **Polling and surveys:** e.g. Pew Research Center (device and internet use by income, age, race, politics), Gallup, industry surveys
- **Government and regulator reports:** FTC (data brokers, surveillance pricing), CFPB, state attorneys general, congressional hearings
- **Marketers and advertisers, in their own words:** audience-segment catalogs from data brokers and ad platforms, ad-industry audience taxonomies, ad-tech documentation and patents, marketing case studies, trade press, conference talks
- **Journalism and investigations:** reporting on how specific companies profile and target people
- **Public datasets** for area data that feeds person-level conclusions: Census ACS by ZIP, County Business Patterns, BLS wages, USDA rural–urban codes

## 4. Evidence types and wording

Every finding records its evidence type, which sets the wording:

| Evidence type | Example source | Supports wording like |
|---|---|---|
| **Research** | Peer-reviewed study, large survey | "You most likely…" / "People with [X] are [N] times more likely to…" |
| **Industry claim** | Data-broker segment, ad-tech patent, marketing case study | "Advertisers characterize people like you as…" / "According to marketers…" |
| **Bad-actor use** | Documented case, regulator report, investigation | "This is the kind of detail used to…" |

The page labels industry and bad-actor statements as *their* view, not a fact about the visitor. Where the industry's basis is weak, say so: "Advertisers place you in [segment] based on nothing more than [signal]."

**Targeting statements go in the profile** when they are **truthful, considered and impactful** (user, devlog 019):
- Fails: "Advertisers know the sun is out where you are." (common knowledge, says nothing about the person)
- Passes: "Because you work in government, you're [three times] more likely to receive targeted ads from lobbying campaigns." (the type of statement wanted; the figure is illustrative, not a claim to verify)

## 5. How to search: three passes

Researching one data group at a time would miss inferences that come from combining groups, and those are usually the strongest. So the research runs in three passes.

### Pass A · Start from the person (main pass)
Begin with what advertisers, data brokers and researchers want to know about someone, and search for *any* signal that predicts each attribute, whatever its group:
- **Money and work:** income, wealth, financial distress, occupation, industry, employer, work schedule
- **Life stage:** age (incl. minors), education, student status, household and family, parent, homeowner or renter
- **Identity ⚑:** ethnicity, immigration and origin, language, religion, politics, gender, sexuality
- **Health ⚑:** health conditions, disability, pregnancy
- **Life events:** moving, a new job, a new relationship or baby, travel, job loss
- **Interests and behavior:** hobbies, purchase intent, media habits, susceptibility to persuasion

Data-broker audience catalogs and the ad industry's audience taxonomy are organized this way, which makes them a natural guide.

### Pass B · Start from the signals (sweep)
Go through the data group by group (list below) to catch correlations Pass A wouldn't think to look for.

### Pass C · Combine
Build a grid of attributes (Pass A) × signals (by group). Any attribute predicted by two or more **independent** groups is a candidate Tier 2 conclusion, e.g. premium hardware (D) + low-income area (I) + a dominant local industry (area data) → likely industry.

### Data groups
Order for Pass B: richest groups first. Full inventory in `data-audit.md` §1–3; the rules that use each group are in `inference-map.md`.

1. **I · Internet connection:** city/ZIP, provider and network type (home, mobile, satellite, campus, school, hospital, library, public Wi-Fi, military, prison, employer), VPN/relay use
2. **D · Device hardware:** phone model and age, iPhone vs. Android, GPU, Apple chip tier, memory, CPU speed, screen and display quality, multiple monitors, battery, data saver, out-of-date software
3. **T · Time and behavior:** time and day of visit, late-night use, attention (tab switching, reading time, scrolling, copying), automation
4. **L / Z · Locale and time zone:** languages and their order, language region, date/number formats, time zone and mismatches
5. **W · Installed software:** fonts (professional, regional, accessibility), text-to-speech voices, keyboard layout
6. **P · Privacy tools:** ad blockers, fingerprint protection, Tor, VPNs, Do Not Track / GPC
7. **A · Accessibility:** contrast, motion, colours, reading-support fonts
8. **H · History:** return visits, referrer, tab history length

For each group: what has been correlated with these signals, alone or combined with other groups.

**New data to watch for:** candidates already noted in `data-audit.md` §3 (typing rhythm, touch and grip, device motion) and §5 (permission-gated), plus anything a source shows is predictive and collectable locally.

## 6. Findings log

Findings go in §9 of this file, one entry each:

- **Data points:** which signals
- **Groups combined:** which evidence groups (one, or several for Tier 2)
- **Inference:** what it says about the person
- **Source:** citation and link
- **Evidence type:** research / industry claim / bad-actor use
- **Reliability of the industry's inference** (industry and bad-actor findings): shown to be accurate / unproven / known to be wrong
- **Strength:** sample, effect size or how widely the claim is made
- **Wording frame:** "you most likely…" / "advertisers characterize you as…" / "used to…"
- **"How did it know" rating:** high / medium / low
- **Sensitivity:** ⚑ category, if any
- **Needs:** data we have / new data to collect / dataset to bundle
- **Candidate rule:** a new Tier 1 or Tier 2 rule, or a change to an existing one

## 7. Secondary track: checking existing rules

From `inference-map.md`:
- **`?` sources:** T1-01 (release-year table), T1-03 (GPU tiers), T1-05 (premium displays), T1-06 (browser versions), T1-07 (CPU baseline), T1-08 (data saver), T1-13, T1-14, T1-17 (Adobe fonts), T1-18, T1-32/33 (provider table), T2-A (combination), T2-B, T2-D (model codes), T2-E
- **✓ sources to check:** Bertrand & Kamenica (2018) for T1-02/T2-A; Census ACS for T1-12/T2-C; Microsoft lifecycle for T1-06; Adobe pricing for T1-17; WebDriver, Tor and browser docs for T1-19, T1-21, T1-22
- **`[STAT]` placeholders:** T1-02 (iPhone/income accuracy), T1-03 (system price), T1-04 (chip price), T1-07 (price band), T1-12 → T2-C (language figures), T1-17 (Adobe price), T2-A (income band), T2-B (prepaid/limited-plan users by income), T2-C (share identifying as Hispanic or Latino)
- **Open questions:** phone replacement cycle (user's figure: 2–4 years); IP location accuracy in the US; smartphone-only internet use by income; T1-14 and T2-E at risk of being cut

## 8. Datasets to bundle

All public; bundled in the page so no visitor data leaves it. From `inference-map.md` Part 5:
- Census ACS by ZIP: income, rent, home ownership, language, industry
- County Business Patterns / BLS: industry wages by area
- Phone model → release year
- Provider table (ISPs, campuses, hospitals, public Wi-Fi, VPN and hosting networks)
- USDA rural–urban codes by ZIP

## 9. Findings

### Pass A · Start from the person (devlog 020)

**How to read these.** Figures come from search summaries of the sources, not full-text reads. Check each figure against the primary source before building (listed at the end of this pass). Each entry gives the §6 fields in a compact form. Abbreviations: **Groups** = evidence groups (§5). **How** = "how did it know" rating. **Rel.** = reliability of an industry or bad-actor inference.

**Strongest candidates** (high "how did it know", supportable with data we have): A-02, A-03, A-06, A-08, A-09, A-10, A-11, A-24, A-35, A-40, plus guard A-15 (fixes an existing rule).

**No supportable signal found** for politics, religion, sexuality, gender, parenthood, job loss, or older age from settings. Politics, religion and sexuality are recorded as bad-actor context only (A-27 to A-29).

#### Money and work

**A-01 · iPhone predicts top-quarter income**
- Data: iPhone/iPad (T1-02) · Groups: D · Inference: you're more likely in the top income quarter
- Source: Bertrand & Kamenica, "Coming Apart? Cultural Distances in the US over Time," *AEJ: Applied* 2023 ([NBER w24771](https://nber.org/papers/w24771)); [Chicago Booth Review](https://www.chicagobooth.edu/review/no-america-not-more-divided-ever)
- Research · Strength: in 2016, owning an iPhone was 69% predictive of high income (iPad 67%), and no brand in any year was more predictive. "Predictive" = how often the item correctly identifies a high-income person, not the share of owners who are high-income. ✓ (source check)
- Frame: "you most likely…" · How: medium · ⚑ finances · Needs: have
- Candidate: confirms T1-02 and the device part of T2-A; source for T1-02's `[STAT]`. Breaks for teens (A-15)

**A-02 · Your phone's operating system predicts whether you repay loans**
- Data: iOS vs. Android (T1-02) · Groups: D · Inference: lenders' models score you as a better or worse credit risk
- Source: Berg, Burg, Gombović & Puri, "On the Rise of FinTechs: Credit Scoring Using Digital Footprints," *Review of Financial Studies* 2020 ([FDIC working paper](https://www.fdic.gov/analysis/cfr/working-papers/2018/cfr-wp2018-04.pdf); [VoxEU summary](https://voxeu.org/article/digital-footprints-and-credit-scoring))
- Research · Strength (✓ read in full): 270,399 furniture purchases, Oct 2015–Dec 2016; average default 0.94%. Android 1.79% vs. iOS 1.07% vs. Mac 0.69%. The iOS–Android gap equals the gap between a median credit score and the 80th percentile. Also: orders placed midnight–6 AM default at 1.97% (about twice average; noon–6 PM buyers are half as likely to default); customers arriving via paid ads default most (1.11%), those typing the URL less (0.84%); a mistyped email address 5.09%; Do Not Track users 0.88% (about average). **German retailer: outside the US-only scope**, included as the founding study for digital-footprint credit scoring
- Frame: "Research used by lenders finds…" · How: high · ⚑ finances · Needs: have
- Candidate: new targeting statement on T1-02: "Lending research finds iPhone users repay loans more reliably than Android users. The gap is about as large as the one between an average credit score and a top-fifth one." Also new: late-night visits (T1-16) and arrival via an ad (T1-29) were both lending risk signals in the same study. Needs a US source before building

**A-03 · Mac users are shown pricier hotels**
- Data: macOS (user agent) · Groups: D · Inference: travel sites treat you as a bigger spender
- Source: Wall Street Journal (Mattioli, 2012), via [NPR](https://www.npr.org/sections/thetwo-way/2012/06/26/155756095/orbitz-shows-mac-users-pricier-hotel-options-big-deal-or-no-brainer) and [ABC News](https://abcnews.com/Travel/mac-users-higher-hotel-prices-orbitz/story?id=16650014)
- Industry claim · Rel.: shown accurate (Orbitz's own data) · Strength: Mac users spent as much as 30% more on hotels and were 40% more likely to book 4–5 star hotels (company figures, via ABC News). Orbitz ranked pricier options first; prices for the same room didn't change
- Frame: "companies…" · How: high · Needs: have
- Candidate: new Tier 1 targeting statement for Mac visitors

**A-04 · Your location sets the price you're shown**
- Data: ZIP from IP (T1-31) · Groups: I (+ ACS income by ZIP) · Inference: you may be paying more because of where you live
- Source: Wall Street Journal 2012 (Staples), via [Consumerist](https://consumerist.com/2012/12/28/staples-com-others-charge-you-different-prices-based-on-where-you-live) and [WTOP](https://wtop.com/news/2012/12/stores-change-online-prices-based-on-shoppers-locations); [Penn study](https://core.ac.uk/download/219378595.pdf)
- Industry practice (journalism, tested from 42,000 ZIPs) · Rel.: shown · Strength: distance to a rival store explained about 90% of the pattern. ZIPs that got discounts averaged $59,900 household income vs. $48,700 for those that didn't, so lower-income areas paid more as a side effect
- Frame: "online stores have…" · How: high · ⚑ finances · Needs: ACS income by ZIP (bundle)
- Candidate: targeting statement attached to T1-31 when the ZIP's income is below the median. About how *you* are priced, so it passes the area-data rule only inside a statement about the person

**A-05 · ZIP pricing ends up as pricing by race**
- Data: ZIP (T1-31) · Groups: I · Inference: geographic pricing charges some groups more
- Source: [ProPublica 2015](https://www.propublica.org/article/asians-nearly-twice-as-likely-to-get-higher-price-from-princeton-review); [Technology Science](https://techscience.org/a/2015090103/)
- Bad-actor / industry use · Rel.: shown · Strength: customers in areas with a high density of Asian residents were 1.8× as likely to be offered higher prices, regardless of income (measured by area, not per customer); one course ranged from $6,600 to $8,400
- Frame: "used to…" · How: medium · ⚑ ethnicity · Needs: ACS by ZIP
- Candidate: supporting example for A-04. Not a person-level statement on its own

**A-06 · Mouse movements and location feed individual pricing**
- Data: live behavior (T1-27), location (T1-31) · Groups: T, I · Inference: what you do on a page can change the price you see
- Source: [FTC surveillance pricing study, initial findings, Jan 2025](https://www.ftc.gov/news-events/news/press-releases/2025/01/ftc-surveillance-pricing-study-indicates-wide-range-personal-data-used-set-individualized-consumer)
- Regulator report · Rel.: documented capability; the FTC's examples are hypothetical, and it made no finding of illegality · Strength: pricing intermediaries say they use location, demographics, browsing history, abandoned carts and mouse movements
- Frame: "pricing firms studied by the FTC…" · How: high · Needs: have
- Candidate: a sentence in T1-27 live behavior: "Pricing firms studied by the FTC use this kind of mouse-movement data to tailor prices."

**A-07 · Smartphone-only internet goes with low income**
- Data: mobile carrier (T1-31/32), phone · Groups: I, D · Inference: you may not have home internet, which goes with low income
- Source: [Pew Research Center, 2025 survey](https://www.pewresearch.org/chart/young-adults-hispanic-adults-those-with-lower-incomes-are-more-likely-to-rely-on-smartphones-for-internet/); [mobile fact sheet](https://www.pewresearch.org/internet/fact-sheet/mobile/)
- Research · Strength (✓, updated): 2026 survey: 29% of adults earning under $30K are smartphone-only, vs. 18% at $30–70K, 12% at $70–100K and 7% at $100K+ (2025: 34/19/10/4). Overall 16%; higher among young and Hispanic adults
- Frame: "you most likely…" (with care) · How: medium · ⚑ finances · Needs: have
- Candidate: source and `[STAT]` for T2-B's planned mobile path. Caveat: being on cellular at home hours doesn't prove there's no home broadband

**A-08 · Data brokers sell lists of financially struggling households**
- Data: whatever fires T2-B · Groups: D (+ I) · Inference: you fit the profile brokers sell to lenders
- Source: [US Senate Commerce Committee, Dec 2013](https://www.commerce.senate.gov/services/files/0d2b3642-6221-4888-a631-08f2f255b577); [FTC, May 2014](https://www.ftc.gov/system/files/documents/reports/data-brokers-call-transparency-accountability-report-federal-trade-commission-may-2014/140527databrokerreport.pdf)
- Industry claim · Rel.: the segments exist; how they're assigned is undisclosed · Strength (✓ both reports read): Senate: "Rural and Barely Making It," "Ethnic Second-City Strugglers," "Retiring on Empty: Singles," "Tough Start: Young Single Parents," "Credit Crunched: City Families," and Experian's "Hard Times" ("the poorest lifestyle segment in the nation"). FTC: "Urban Scramble" and "Mobile Mixers" (high concentration of Latinos and African Americans with low incomes), "Rural Everlasting," "Thrifty Elders," "Financially Challenged"
- Frame: "data brokers have sold lists of people like you as…" · How: high · ⚑ finances · Needs: have
- Candidate: targeting sentence in T2-B using confirmed names only

**A-09 · Naming your employer from your IP address is a product**
- Data: organization from IP (T1-32 "anything else") · Groups: I · Inference: the site knows where you work
- Source: vendor material: [Clearbit](https://clearbit.com/blog/ip-intelligence-for-remote-work), [Demandbase](https://www.demandbase.com/blog/why-company-identification-is-trickier-than-you-think/), [Leadfeeder](https://leadfeeder.com/blog/website-visitor-identification/how-to-identify-anonymous-website-visitors), [Abmatic](https://abmatic.ai/blog/reverse-ip-lookup-guide-2026)
- Industry claim · Rel.: varies. Vendors claim a confident company match for roughly 10–40% (Leadfeeder) to 35–50% (Abmatic) of business-site sessions. Demandbase calls registry data inaccurate
- Frame: "sales software does this…" · How: high · Needs: have
- Candidate: sentence in T2-G: "Sales software sold to websites does exactly this: it names the company behind a visit so a sales team can follow up."

**A-10 · Government networks are an ad audience for lobbyists** (the user's example)
- Data: government organization from IP (new provider type) · Groups: I · Inference: you're on a government network and the audience lobbying campaigns pay to reach
- Source: [Rational 360](https://rational360.com/?p=531) (IP-matched campaigns aimed at state legislators and staff); [FiscalNote](https://fiscalnote.com/products/audience-extension-advertising) (congressional-staff segments with IP targeting); Last Week Tonight 2022, which reached at least three IPs inside the Capitol ([National Law Review](https://natlawreview.com/article/last-week-tonight-host-pokes-congress-over-data-brokering-regulation)); critique in [ClickZ](https://clickz.com/?p=48267)
- Industry claim · Rel.: shown to work. No figure found for "N times more likely"
- Frame: "advocacy firms sell…" · How: high · Needs: add a `government` type to the provider table (federal, state, `.gov` organizations)
- Candidate: new T1-32 network type plus targeting statement: "Lobbying and advocacy firms sell ad campaigns aimed at the IP addresses of government buildings. On this network, you're who they're paying to reach."

**A-11 · Military networks: data sold cheaply, members targeted by predatory lenders**
- Data: military network (T1-32) · Groups: I · Inference: you're likely a servicemember, and so a target
- Source: [Duke, Sherman et al. 2023](https://techpolicy.sanford.duke.edu/wp-content/uploads/sites/4/2023/11/Sherman-et-al-2023-Data-Brokers-and-the-Sale-of-Data-on-US-Military-Personnel.pdf); [CFPB](https://www.consumerfinance.gov/about-us/blog/seeing-servicemembers-as-dollar-signs-in-uniform); [PIRG / CFPB complaints](https://pirg.org/resources/protecting-those-who-serve/)
- Bad-actor use + research · Rel.: shown · Strength: brokers sold servicemember records for $0.12–0.32 each; data on offer included names, home addresses, geolocation, net worth and religion (✓ MIT Technology Review). The DoD 2006 finding and lenders clustering near bases are unverified (the CFPB page supports only for-profit colleges targeting servicemembers)
- Frame: "used to…" · How: medium–high · ⚑ finances · Needs: have
- Candidate: targeting sentence on T1-32's military branch

**A-12 · Night-shift occupations**
- Data: time of visit (T1-16) + network type · Groups: T (+ I) · Inference: you may work nights
- Source: [NIOSH analysis of ATUS 2011–2020](https://stacks.cdc.gov/view/cdc/208173); [BLS ATUS](https://www.bls.gov/tus/tables/a4-1519.htm)
- Research (base rates only) · Strength: 41.7% of protective-service workers and 28.6% of food-service workers often work non-day shifts; about 3% of full-time workers are at work at 2 AM. **No study links browsing times to occupation**
- Frame: "you may…" · How: high only when combined · Needs: have
- Candidate: Pass C. A hospital network at night means a patient, a visitor or night staff (with A-35). Not a statement from time alone

**A-13 · Weekday daytime at home has too many readings**
- Data: residential ISP + computer + weekday 9–5 · Groups: I, T · Inference: none reliable
- Source: [WFH Research (SWAA) 2025](https://siepr.stanford.edu/publications/essay/working-home-2025-five-key-facts)
- Research · Strength: about a quarter of US paid workdays are work-from-home (7 points higher for parents of children under 8). Remote worker, unemployed, retired, student and shift worker all fit
- How: low · Candidate: **none**. Recorded so it isn't proposed later

**A-14 · Hiring algorithms rated people by their browser**
- Data: browser · Groups: D · Inference: an employer's software scored you as a better or worse hire
- Source: Evolv/Cornerstone, via [Smithsonian](https://www.smithsonianmag.com/smart-news/could-your-browser-make-you-better-employee-180954640/) and [Nextgov](https://www.nextgov.com/modernization/2015/03/if-you-use-firefox-or-chrome-youre-better-employee/107598/)
- Industry claim · Rel.: unproven (no causal finding, by the company's own account) · Strength: about 50,000 people hired after an online assessment; those who used a non-default browser such as Chrome or Firefox stayed about 15% longer and performed better. **Dated** (Internet Explorer era); the distinction means little now
- Frame: "based on nothing more than…" · How: medium · Needs: have
- Candidate: an example of a weak-basis inference, possibly for an explanatory section rather than a per-visitor statement

#### Life stage

**A-15 · Most US teens use iPhones, so iPhone ≠ income for young visitors**
- Data: iPhone (T1-02) · Groups: D · Inference: guard against a false income reading
- Source: Piper Sandler "Taking Stock With Teens," spring 2025 ([MacRumors](https://www.macrumors.com/2025/04/09/teen-iphone-ownership-continues-to-soar/)); [Pew, Oct 2025](https://www.pewresearch.org/internet/2025/10/08/how-parents-manage-screen-time-for-ki)
- Industry survey (n = 6,455) + research · Strength: 88% of US teens own an iPhone. Among kids 12 and under, lower-income parents are *more* likely to say their child has a smartphone (31% vs. 16% for upper-income)
- How: n/a (guard) · ⚑ minors · Needs: have
- Candidate: **fix to T2-A**. Don't read income from an iPhone on a school network or during school hours

**A-16 · Declined (devlog 021).** Detecting children from touch input. The page doesn't collect or reflect anything that singles out under-18s (`PRODUCT.md` → Minors). Don't propose again.

**A-17 · Age and gender from mouse movement and typing**
- Data: mouse dynamics (B), typing (new data) · Groups: T · Inference: age, gender
- Source: [Univ. of Delaware](https://www.eecis.udel.edu/~hnw/paper/gender16.pdf) (gender 89% in lab); [WWW 2016 companion](https://archives.iw3c2.org/www2016/WWW2016-Proceedings/companion/p61.pdf) (free web browsing: 60–67% precision); keystroke studies (age group 66–88%)
- Research · Strength: strong in lab, weak on real web pages
- How: low as built · Candidate: **none**. Doesn't change the Standards decision to leave gender out

**A-18 · Platforms already estimate age from behavior**
- Source: [Google, July 2025](https://blog.google/technology/safety-security/age-assurance-measures-safer-online-kids-teens-us/); [TechCrunch](https://techcrunch.com/2025/07/31/google-is-experimenting-with-machine-learning-powered-age-estimation-tech-in-the-u-s)
- Industry practice · Google estimates US users' age from search topics, video categories and account age; users it flags lose personalized ads
- Candidate: context only. Not our data

**A-19 · Older adults: no settings signal found**
- Source: [Pew mobile fact sheet](https://www.pewresearch.org/internet/fact-sheet/mobile/), [Pew short read, Jan 2026](https://www.pewresearch.org/short-reads/2026/01/08/internet-use-smartphone-ownership-digital-divides-in-u-s/)
- 79% of adults 65+ own a smartphone; 70% have home broadband. **No source links larger text, zoom or contrast to age**
- Candidate: none (gap)

**A-20 · Life-stage segments brokers sell**
- Source: Experian ([Mosaic](https://www.experian.com/marketing-knowledge-base/data/experian-mosaic), [syndicated audiences](https://www.experian.com/blogs/marketing-forward/reach-every-financial-consumer-with-experian-audiences/page/12))
- Industry · "New Parents: Child Aged 0–36 Months," "New Movers: High Spenders," Mosaic's 71 household types (e.g., "Promising Families")
- Candidate: none alone. No signal on this page detects parenthood

#### Identity ⚑

**A-22 · Spanish at home → about 92% Hispanic or Latino**
- Data: language (T1-11) + W · Groups: L, W · Inference: likely Hispanic or Latino
- Source: [Pew analysis of 2011 ACS](https://www.pewresearch.org/short-reads/2013/08/13/spanish-is-the-most-spoken-non-english-language-in-u-s-homes-even-among-non-hispanics/); [Census 2018–22](https://census.gov/newsroom/press-releases/2023/language-at-home-acs-5-year.html)
- Research · Strength: 34.8M Hispanic vs. 2.8M non-Hispanic Spanish speakers ≈ 92–93% (my arithmetic, 2011 data)
- Frame: "you most likely…" · How: medium · ⚑ ethnicity · Needs: a current ACS cross-tab
- Candidate: `[STAT]` for T2-C

**A-23 · Scammers single out Spanish-speaking communities**
- Data: language (T2-C) · Groups: L, W · Inference: you're the kind of target certain scams aim at
- Source: [FTC, Latino communities](https://www.militaryconsumer.gov/scam-alerts/protecting-every-community-update-ftcs-work-combat-fraud-targeting-latinos); [FTC Serving Communities of Color, 2021](https://www.ftc.gov/system/files/documents/reports/serving-communities-color-staff-report-federal-trade-commissions-efforts-address-fraud-consumer/ftc-communities-color-report_oct_2021-508-v2.pdf)
- Bad-actor use · Strength: impersonation scams are the top report from majority-Latino areas, with more job and business-opportunity scams than elsewhere and more payment by cash or crypto. Under-reported
- Frame: "used to…" · How: medium · ⚑ · Needs: have
- Candidate: targeting sentence in T2-C

**A-24 · Platforms have labeled ethnicity from behavior and let advertisers exclude by it**
- Data: language and other signals (T2-C) · Groups: L, W · Inference: ad systems guess your ethnicity, on a weak basis
- Source: [ProPublica 2016](https://www.propublica.org/article/facebook-lets-advertisers-exclude-users-by-race); [TechCrunch](https://techcrunch.com/2016/10/28/facebook-ethnic-affinity/)
- Industry practice · Rel.: unproven. Facebook assigned "Ethnic Affinity" from pages people liked or engaged with; ProPublica's ad excluding Black, Asian and Hispanic affinities from a housing category was approved in 15 minutes
- Frame: "based on nothing more than…" · How: high · ⚑ · Needs: have
- Candidate: targeting sentence in T2-C

**A-25 · Immigration enforcement buys advertising and location data**
- Data: international model (T2-D), language, location · Groups: D, L, I · Inference: details like these are what that data contains
- Source: [ACLU FOIA](https://www.aclu.org/cases/aclu-v-department-homeland-security-commercial-location-data-foia); [Axios, Oct 2025](https://www.axios.com/2025/10/21/ice-surveillance-trump-deportations); [The Register, Jan 2026](https://www.theregister.com/2026/01/27/ice_data_advertising_tech_firms/)
- Bad-actor / government use · Rel.: shown (purchases documented). CBP/ICE bought from Venntel and Babel Street; ICE bought PenLink's tool in 2025 and in Jan 2026 asked for ad-tech data. **No source says they filter by phone model**
- Frame: "this is the kind of data…" · How: high · ⚑⚑ immigration · Needs: have
- Candidate: sentence in T2-D, if the user decides it's appropriate. Wording must stay at "this kind of data"

**A-26 · Phone replacement cycle about 2.5 years** (secondary track, T2-D timeframe)
- Source: Statista forecast via [BankMyCell](https://www.bankmycell.com/blog/average-lifespan-of-smartphone); [YouGov, Nov 2025](https://yougov.com/en-us/articles/53648-americans-plan-faster-smartphone-upgrades-and-fewer-expect-to-buy-budget-models)
- Industry forecast · 2.49 years for US consumers (2025). Buyers intending to keep phones 1–2 years rose from 15% to 25% since 2023
- Candidate: loosely supports the user's 2–4 year figure. A CIRP or Counterpoint US figure would be better

**A-27 · Politics: no reliable device signal**
- Hunch 2011 (iPhone leans liberal, mostly-iPhone sample), DeviceAtlas (state-level only), Velti/Harris 2012 (roughly even: 47% vs. 50%) ([PhoneArena](https://www.phonearena.com/news/iOS-vs-Android-by-State-Republicans-vs-Democrats-iPhone_id110857), [NBC](https://www.nbcnews.com/news/world/smartphone-users-prefer-obama-survey-says-flna876214))
- Weak, dated, contradictory · Candidate: **none** (fails "truthful")

**A-28 · Religion: bad-actor context only**
- The US military bought location data from the Muslim Pro prayer app through X-Mode ([Vice 2020](https://www.vice.com/en/article/muslim-pro-location-data-military-xmode)); brokers sell servicemembers' religion (A-11)
- No signal on this page supports a religion inference (a language is not a religion) · Candidate: none

**A-29 · Sexuality: bad-actor context only**
- "Commercially available app signal data" used to out a priest via Grindr ([Gizmodo](https://gizmodo.com/a-priest-was-outed-by-his-phones-location-data-anyone-1847334277)) · No signal here supports it; Standards keep it out · Candidate: none

#### Health ⚑

**A-30 · High contrast: a base rate for T2-E**
- Data: forced colors, contrast (T1-24) · Groups: A · Inference: low vision (weak)
- Source: Microsoft figure via [W3C CSSWG 2019](https://lists.w3.org/Archives/Public/www-archive/2019Feb/att-0002/CSSWG_F2F_High_Contrast.pdf) and [Smashing](https://smashingmagazine.com/2022/06/guide-windows-high-contrast-mode/); [WebAIM low-vision survey 2018](https://webaim.org/projects/lowvisionsurvey2) (n = 248)
- Research · Strength: High Contrast Mode is on about 4% of active Windows machines; 51% of low-vision respondents use some high-contrast mode. W3C is still debating the privacy of these queries ([2025 issue](https://lists.w3.org/Archives/Public/public-css-archive/2025Jun/0163.html))
- How: medium · ⚑ disability · Candidate: keeps T2-E alive with weak wording ("about half of people with low vision use this setting"). No data for reduced motion

**A-31 · Dyslexia fonts: no usage data** — prevalence 3–7% (strict) to 5–15% ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC8183124)); nothing on who installs OpenDyslexic. T1-25 stays unsourced

**A-32 · Tremor visible in mouse movements**
- Data: mouse path (B) · Groups: T · Inference: a motor condition (not to be built)
- Source (✎ citation corrected): White, Doraiswamy & Horvitz, "Detecting neurodegenerative disorders from web search signals," *npj Digital Medicine* 2018 ([PMC6550228](https://pmc.ncbi.nlm.nih.gov/articles/PMC6550228); [Duke Scholars](https://scholars.duke.edu/publication/1468380)); White & Horvitz, "Population-scale hand tremor analysis via anonymized mouse cursor signals," *npj Digital Medicine* 2019 ([PMC6760188](https://pmc.ncbi.nlm.nih.gov/articles/PMC6760188))
- Research · Strength: 31,321,773 Bing users; features included cursor-position oscillation; Parkinson's sensitivity 94.2% at a 20% false-positive rate. The 2019 paper's cursor tremor estimates aligned with self-reports and clinical findings. Low sampling resolution limits it
- Frame: capability, not a diagnosis · How: high · ⚑ health
- Candidate: possibly a general sentence alongside T1-27 ("Researchers have screened for Parkinson's from mouse movements like the ones this page recorded"). Never a per-visitor health claim

**A-33 · Late-night use and mental health**
- Source: adolescent study, midnight–5 AM use linked to shorter sleep and more internalizing symptoms ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/pmid/29280394)), self-reported
- Research, weak for an individual · ⚑ · Candidate: **none** (too strong for T1-16)

**A-34 · Brokers sell mental-health lists**
- Source: [Duke, Kim 2023](https://techpolicy.sanford.duke.edu/data-brokers-and-the-sale-of-americans-mental-health-data/)
- Industry · 11 of 37 brokers would sell data on depression, anxiety, bipolar disorder, PTSD and more; some lists included names and addresses ("Anxiety Sufferers")
- Candidate: context only. Not linked to any signal here

**A-35 · Hospital networks and leaked health data**
- Data: hospital network (T1-32) · Groups: I (+ T) · Inference: you're at a hospital; hospital sites have leaked visitors' details to ad platforms
- Source: [The Markup, "Pixel Hunt," 2022](https://themarkup.org/pixel-hunt/2022/06/16/facebook-is-receiving-sensitive-medical-information-from-hospital-websites)
- Bad-actor / industry use · Rel.: shown · Strength: the Meta Pixel was on 33 of the top 100 US hospital sites, sending appointment clicks, doctor names and conditions; also inside 7 patient portals
- Frame: "used to…" · How: medium–high · ⚑ health · Needs: have
- Candidate: sentence on T1-32's hospital branch. Pass C: hospital network at night (A-12)

**Pregnancy (context):** Target scored shoppers for pregnancy from about 25 products and estimated due dates (Duhigg, *NYT Magazine* 2012, via [HealthWorks Collective](https://www.healthworkscollective.com/target-knows-you-re-pregnant-even-if-no-one-else-knows)). No signal here.

#### Life events

**A-36 · New movers are the life event marketers most want**
- Data: possibly a clock vs. connection mismatch (T1-34) · Groups: Z, I · Inference: a recent move (weak)
- Source: vendors: [Porch Group Media](https://porchgroupmedia.com/blog/top-items-new-movers-purchase-in-the-first-12-months/), [speedeon](https://speedeondata.com/?p=35063); Experian (13M movers a year)
- Industry claim · Rel.: figures inconsistent (Porch Group's page now says about $11,000 during a move; the $8,068 figure wasn't found); most buying decisions fall within 2 months of a move
- How: medium · Candidate: weak. A possible extra reading for T1-34, not a statement on its own

**A-37 · Prices change by where you're connecting from and on what**
- Data: location (T1-31, T2-I), phone vs. computer · Groups: I, D
- Source: [Hannak et al., IMC 2014](https://khoury.northeastern.edu/home/ancsaaa/files/imc151.pdf); [2017 follow-up (arXiv 1712.03031)](https://arxiv.org/pdf/1712.03031.pdf)
- Research · Strength (✓ papers read): personalization on 9 of 16 sites; Cheaptickets and Orbitz gave logged-in "members" cheaper hotels; Home Depot and Travelocity personalized results for mobile browsers (the $230 vs. $120 figure is from press coverage, not the paper). Hupperich et al. 2017, "An Empirical Study on Price Differentiation Based on System Fingerprints," found location-based price differences
- Frame: "sites have been found to…" · How: medium · Needs: have
- Candidate: sentence in T2-I (traveling) and with T1-01 (phone)

**Job loss, new job:** no source links any signal here. Gap.

#### Interests and behavior

**A-39 · Advertisers' picture of ad-blocker users**
- Data: ad blocker (T1-20) · Groups: P
- Source: GWI via [Backlinko](https://backlinko.com/ad-blockers-users) and [Press Gazette](https://pressgazette.co.uk/marketing/ad-blockers-mean-publishers-missing-out-on-ad-revenue-from-one-in-five-readers/); [Ghostery/Censuswide 2024](https://ghostery.com/blog/privacy-report-advertisers-and-adblockers)
- Industry claim · Rel.: unproven for an individual · Strength: 23% (GWI Q2 2024) to 32.5% (GWI Q2 2025) of US users, 52% (Ghostery). The "men 25–34" and "young men with a high level of education" descriptions are global, not US
- Frame: "advertisers characterize…" · How: low–medium · Candidate: optional sentence on T1-20

**A-40 · A dying battery means you'll pay more**
- Data: battery level and charging (D, Chromium only) · Groups: D · Inference: you're more willing to pay right now
- Source: Uber's head of economic research, Keith Chen, on NPR *Hidden Brain*, 2016 ([TechRadar](https://techradar.com/news/car-tech/you-re-more-likely-to-pay-uber-s-surge-prices-on-a-low-battery-1321900), [Quartz](https://qz.com/689060/uber-has-pinpointed-the-moment-youre-most-likely-to-pay-for-surge-pricing))
- Industry claim · Rel.: shown in Uber's own data; Uber says it doesn't use it to set fares · Strength: "one of the strongest predictors" of accepting surge pricing
- Frame: "Uber found…" · How: high · Needs: have
- Candidate: new Tier 1 when battery is low and not charging: "Uber found people with a dying battery are more willing to pay surge prices. Yours is at [12%]."

**A-41 · Late-night shopping is more impulsive (industry surveys)**
- Data: late night (T1-16) · Groups: T
- Source: [CouponFollow 2026](https://couponfollow.com/research/late-night-shopping-costs) (n = 1,010: 39% buy on impulse late at night, 47% overspent); PayPal (Australia)
- Industry survey · Rel.: unproven; no peer-reviewed confirmation
- How: low–medium · Candidate: optional targeting sentence on T1-16

**A-42 · Personality-matched ads**
- Source: Matz et al., *PNAS* 2017 ([PubMed](https://pubmed.ncbi.nlm.nih.gov/29133409/)); critiques ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6112709))
- Research, contested · Up to 1.5× purchases when ads matched personality inferred from Facebook Likes
- Candidate: none. No personality signal here

#### To check in primary sources before building

- Berg et al. (A-02): whether time of purchase or Do Not Track also predicted default; a US equivalent
- Bertrand & Kamenica (A-01): which device the 69% belongs to and the exact definition
- FTC 2014 Appendix B (A-08): confirm segment names
- Current ACS cross-tab of Spanish speakers by Hispanic origin (A-22)
- Original WSJ articles (A-03, A-04) and Hannak 2017 (A-37)
- CIRP or Counterpoint US replacement cycle (A-26)

### Pass B · Start from the signals (devlog 022)

Same format and caveats as Pass A (figures come from search summaries). Signals already covered in Pass A are cross-referenced, not repeated.

**Strongest new candidates:** B-01, B-03, B-14, B-18, B-20, B-23. **Guards on existing rules:** B-04, B-08. **Secondary track (sources for existing rules):** B-02, B-06, B-10, B-11, B-26.

#### I · Internet connection

**B-01 · Same price, slower internet in lower-income neighborhoods**
- Data: provider (T1-32) + ZIP (T1-31) · Groups: I (+ ACS income by ZIP) · Inference: your provider likely offers you a worse deal than richer neighborhoods get
- Source: [The Markup, "Dollars to Megabits," 2022](https://themarkup.org/still-loading/2022/10/19/dollars-to-megabits-you-may-be-paying-400-times-as-much-%20as-your-neighbor-for-internet-service); [methodology](https://themarkup.org/show-your-work/2022/10/19/how-we-uncovered-disparities-in-internet-deals)
- Industry practice (journalism) · Rel.: shown; AT&T called it "fundamentally flawed" · Strength: over 1.1 million offers from AT&T, Verizon, EarthLink and CenturyLink in 45 cities; the income analysis covered 38 cities. Where speeds varied, lower-income neighborhoods got worse deals in 92% of cities, and people of color in 66%. Historically redlined areas fared worst
- Frame: "your provider has been found to…" · How: high · ⚑ finances · Needs: ACS income by ZIP
- Candidate: targeting statement when the provider is one of the four and the ZIP's income is below the city's median

**B-02 · How accurate IP location is in the US** (secondary track, T1-31)
- Source: [MaxMind accuracy page](https://www.maxmind.com/en/city_accuracy); [arXiv 2105.13389](https://arxiv.org/pdf/2105.13389.pdf)
- Research + vendor · About 62% of US IPs are placed within 50 km and 31% in the wrong city (MaxMind GeoIP City, undated). Saxon & Feamster 2021: home connections are more accurate (median 2.62 km in NYC with MaxMind's **paid** GeoIP2; paid versions often beat free ones, and this page uses free GeoLite2 data); mobile and business networks are worse, universities better
- Candidate: figure for T1-31's "within X miles". Supports state-only wording for mobile carriers (devlog 018) and suggests the same caution for business networks

**B-03 · Satellite internet means rural**
- Data: provider = Starlink / satellite (new provider type) · Groups: I · Inference: you most likely live somewhere rural, without cable or fiber
- Source: Recon Analytics and New Street Research via [Broadband Breakfast](https://broadbandbreakfast.com/study-with-2-7-million-subscribers-starlink-is-a-top-10-u-s-isp/) and [Fierce](https://www.fierce-network.com/broadband/starlink-outshines-cable-reliable-service-mostly-rural-footprint); urban growth per [Ookla via Fierce](https://www.fierce-network.com/broadband/starlinks-us-broadband-users-are-moving-beyond-rural-america-ookla)
- Industry research · Strength (two studies): Recon Analytics 2024: 85% rural; most switched from a small rural provider or had none; 11% new to home internet. New Street Research 2026: 2.7M US subscribers, over 85% from mostly rural areas, 20% former cable customers. Urban share is growing
- Frame: "you most likely…" · How: medium · Needs: add satellite providers to the provider table
- Candidate: new T1-32 network type

**B-04 · Fiber no longer signals income** (guard)
- Source: Cowen survey via [Fierce](https://fierce-network.com/broadband/fiber-increasingly-affordable-option-residential-consumers-cowen)
- Industry survey (n > 1,200) · Fiber subscribers averaged about $83,000 household income vs. $85,000 for non-subscribers
- Candidate: **guard**. Don't use a fiber provider as an income signal

**B-05 · VPNs are common now**
- Source: [Security.org](https://www.security.org/resources/vpn-consumer-report-annual/), [NordVPN survey](https://nordvpn.com/blog/nordvpn-usage-survey-us/), [All About Cookies](https://allaboutcookies.org/vpn-usage-survey)
- Commercial surveys · 42% of US adults use a VPN for personal or work purposes, up from 32% in 2025 (Security.org, Aug 2026, n = 1,008); higher among adults under 30
- Candidate: wording check on T1-33. A VPN alone isn't unusual, so T2-F should keep needing a second privacy signal (it does)

**B-06 · Who uses library internet** (secondary track, T2-B library path)
- Source: [Pew 2016](https://www.pewresearch.org/internet/2016/09/09/library-usage-and-engagement/), [Pew 2015](https://www.pewresearch.org/internet/2015/09/15/who-uses-libraries-and-what-they-do-at-their-libraries/), [Pew 2013](https://www.pewresearch.org/internet/2013/01/22/part-3-technology-use-at-libraries/)
- Research, dated · People using library computers and Wi-Fi skew young, Black, female and lower income (2016). The 2013 report points the other way on income
- Candidate: weak support for T2-B's library path; needs a newer source

#### D · Device hardware

**B-07 · Mac owners: higher income and education** (supports A-03, T2-A)
- Source: NPD 2009 and Hunch 2011 via [GOOD](https://www.good.is/articles/the-real-difference-between-macs-and-pcs-macs-are-much-richer) and [Digital Trends](https://www.digitaltrends.com/computing/mac-vs-pc-user-differences-fit-stereotypes-study-shows/)
- Industry surveys, dated, self-selected · 36% of Mac users reported household income over $100K vs. 21% of PC users (2009). 67% vs. 54% had a college degree (2011)
- Candidate: possible device path for T2-A (Mac + another group). Needs a current source

**B-08 · Android is not a low-income signal** (guard)
- Source: [Pew 2013](https://pewresearch.org/short-reads/2013/06/29/as-it-turns-6-a-look-at-who-uses-the-iphone-no-not-everybody); Bertrand & Kamenica via [TechSpot](https://www.techspot.com/community/topics/owning-an-iphone-is-the-best-indicator-of-wealth-claims-research.247673); App Science Labs via [Marketing Dive](https://www.marketingdive.com/press-release/20190716-sabios-app-science-labs-finds-apps-location-phone-price-mobile-indica)
- Research · Android ownership was roughly flat across incomes (Pew); owning Android predicted income only weakly (59.5%, near chance). Phone *price* tracks a ZIP code's income (press release only)
- Candidate: **guard**. Never infer low income from Android alone; T1-07 (memory ≤ 2 GB) stays the low-cost signal

**B-09 · Prepaid plans: not detectable**
- Source: [Survey Practice 2025](https://www.surveypractice.org/article/150272-bridging-the-gap-harnessing-prepaid-cell-samples-to-reach-traditionally-underrepresented-demographics); [YouGov](https://business.yougov.com/content/36097-closer-look-pre-paying-mobile-consumer)
- Research · Prepaid users skew younger, lower-income (half earn under 75% of the median), renters, Hispanic and Black
- Candidate: none. Prepaid brands (Metro, Cricket, Boost) usually appear under their parent network's name, so the page can't tell them apart. Check the ASN data before ruling it out completely

**B-10 · Windows 10 after end of support** (secondary track, T1-06)
- Source: [StatCounter US](https://gs.statcounter.com/windows-version-market-share/desktop/united-states-of-america/); HP via [Windows Latest](https://windowslatest.com/2026/08/11/5-years-later-windows-10-refuses-to-die-and-microsoft-just-cant-push-holdouts-to-windows-11)
- About 15% of US desktop Windows page views still come from Windows 10 (StatCounter, Oct 2026; Windows 11 84%); HP says at least 3 in 10 PCs in its installed base run it
- Candidate: `[STAT]` for T1-06 ("about 1 in 7 US Windows users"). Not an income signal on its own

**B-11 · Data saver is rare in the US** (secondary track, T1-08)
- Source: [Tim Kadlec 2019](https://timkadlec.com/remembers/2019-08-29-save-data-usage); [DebugBear](https://www.debugbear.com/blog/save-data-adoption)
- Industry data, dated · 20% of Shopify requests from India and Brazil carried the header; about 4% of sessions on one site, 1.7% in DebugBear's sample (the 28% worldwide figure wasn't confirmed and is dropped)
- Candidate: in the US it's an unusual setting, which supports T1-08 as a deliberate choice. Also weak support for T2-D (a device set up abroad)

**B-12 · Gaming hardware: no demographic source.** The Steam survey records hardware only. Gap.

Battery: see A-40.

#### T · Time and behavior

**B-14 · Popular sites record this same behavior**
- Data: mouse, scroll, typing (B recorder, T1-27) · Groups: T · Inference: about what's done with this kind of data
- Source: Princeton CITP, ["No Boundaries," 2017](https://blog.citp.princeton.edu/2017/11/15/no-boundaries-exfiltration-of-personal-data-by-session-replay-scripts); [follow-up, 2018](https://blog.citp.princeton.edu/2018/01/12/website-operators-are-in-the-dark-about-privacy-violations-by-third-party-scripts)
- Research · Rel.: shown · Strength: session-replay scripts on 482 of the top 50,000 sites, and recording seen on 8,000 sites in the data release. They capture keystrokes, mouse movement and scrolling; some captured passwords and medical details
- Frame: "this is the kind of data…" · How: medium–high · Needs: have
- Candidate: sentence in T1-27, alongside A-06

**B-15 · Night owls and personality: too weak**
- Source: Buzoku et al. 2025 meta-analysis; [Lan et al. 2024](https://pmc.ncbi.nlm.nih.gov/articles/PMC11348206)
- Research · Evening types score slightly lower on conscientiousness; other traits near zero. One late visit doesn't establish a chronotype
- Candidate: **none**

**B-16 · Reading speed and reading level**
- Data: reading time, scrolling (B) · Groups: T
- Source: [Google Research 2021](https://www.research.google/blog/predicting-text-readability-from-scrolling-interactions/) (518 participants)
- Research · Scroll speed and read time differed between beginner, intermediate and advanced readers. **No link to formal education found**
- How: low · ⚑ education · Candidate: none now. Would need text of known difficulty on the page

**B-17 · Cursor hesitation**
- Source: [AMCIS 2015](https://aisel.aisnet.org/amcis2015/HCI/GeneralPresentations/8); [Fisher, *Management Science* 2023](https://pubsonline.informs.org/doi/fpi/10.1287/mnsc.2022.4598)
- Research · Cursor paths show when price starts to affect a choice. Uncertainty peaks at *moderate* hesitation, so "more hesitation = less sure" is wrong
- Candidate: background for A-06. Not a statement on its own

#### L / Z · Locale and time zone

**B-18 · A clock that doesn't match your connection is a fraud flag**
- Data: time zone vs. connection time zone (T1-34) · Groups: Z, I · Inference: fraud systems would score you as riskier right now
- Source: [Microsoft Dynamics 365 Fraud Protection](https://learn.microsoft.com/ro-ro/dynamics365/fraud-protection/df-attribute); [Fingerprint](https://docs.fingerprint.com/docs/geolocation); [SEON](https://seon.io/resources/real-time-risk-data-fraud-prevention/)
- Industry practice · Rel.: shown (documented features) · The clock-vs.-IP time zone mismatch is a standard field in fraud scoring and is treated as a sign of masking
- Frame: "fraud-detection systems…" · How: high · Needs: have
- Candidate: sentence in T1-34: "Fraud-detection systems flag exactly this mismatch, so a purchase made now is more likely to face extra checks."

**B-19 · Ad systems read your language setting**
- Source: [Google Ads language targeting](https://support.google.com/google-ads/answer/1722078); [Campaign Manager 360](https://support.google.com/campaignmanager/answer/7008193)
- Industry · Browser language is one signal Google Ads uses (alongside location and history). Campaign Manager 360 serves only to browsers set to the chosen languages. Google Search drops its campaign language setting in Sept 2026
- How: low–medium · Candidate: optional clause on T1-11

**B-20 · A Spanish-first browser suggests an immigrant**
- Data: Spanish as first language (T1-11) · Groups: L (+ W, I) · Inference: you're more likely an immigrant than US-born
- Source: [Pew 2012, language use among Latinos](https://www.pewresearch.org/hispanic/2012/04/04/iv-language-use-among-latinos/); [Pew 2023](https://www.pewresearch.org/race-and-ethnicity/2023/09/20/latinos-views-of-and-experiences-with-the-spanish-language/)
- Research, dated · Strength: 61% of immigrant Hispanic adults are Spanish-dominant, vs. 8% of the second generation; 69% of the third generation are English-dominant. Assumes a Spanish-first browser ≈ Spanish-dominant, which needs care
- Frame: "you're more likely…" · How: high · ⚑ immigration · Needs: have
- Candidate: strengthens T2-C and T2-D. Needs a current Pew figure and the user's call on wording

#### W · Installed software

**B-21 · Fonts reveal software**
- Source: [Fifield & Egelman 2015](https://rd.springer.com/chapter/10.1007/978-3-662-47854-7_7); [John D. Cook](https://www.johndcook.com/blog/2019/02/04/font-fingerprinting/)
- Research + commentary · Font sets reveal installed software. Common software (Office) identifies little; rare software (e.g., Mathematica) identifies a lot
- Candidate: supports the method behind T1-17/T1-18. **Still no source linking particular fonts to occupation**. Gap

Voices and keyboard layout: no new sources beyond Pass A.

#### P · Privacy tools

**B-22 · Do Not Track is ignored and makes you easier to single out**
- Source: [EFF](https://www.eff.org/issues/do-not-track-archived); [Cover Your Tracks](https://coveryourtracks.eff.org/about); [Open University](https://www.open.edu/openlearncreate/mod/page/view.php?id=201467)
- Commentary · Most sites and trackers ignore DNT (EFF, Cover Your Tracks); DNT is one of the attributes it measures. Eckersley 2010 (✓): at best 1 in 286,777 browsers share a fingerprint; 83.6% were unique
- How: medium · Candidate: wording for T1-23's DNT branch

**B-23 · Global Privacy Control is legally binding in some states**
- Data: GPC (T1-23) + state (T1-31) · Groups: P, I · Inference: in your state, sites must treat this as an opt-out of data sales
- Source: [California AG, Sephora settlement 2022](https://oag.ca.gov/node/556528); [CPPA 2026](https://cppa.ca.gov/meetings/materials/20260806_07_02.pdf); [Goodwin, 2025 multistate sweep](https://www.goodwinlaw.com/en/insights/publications/2025/09/alerts-technology-dpc-multistate-privacy-enforcement-sweep)
- Regulator action · Rel.: shown · Strength: Sephora paid $1.2M for ignoring GPC. California, Colorado and Connecticut ran a joint GPC enforcement sweep in 2025 and require honoring it; reports put the total at about a dozen states (Delaware, Montana, Nebraska named in secondary sources; full list unverified)
- Frame: observed + law · How: medium–high · Needs: a state list (bundle)
- Candidate: state-specific sentence in T1-23; list to verify against each statute

**B-24 · Searching for privacy tools got people flagged**
- Source: XKeyscore reporting, 2014 ([Schneier](https://www.schneier.com/blog/archives/2014/07/nsa_targets_pri.html), [PCWorld](https://www.pcworld.com/article/2450940/secret-code-indicates-nsa-tracks-users-of-privacy-tools-report-says.html))
- Government use, dated, source unconfirmed · NSA rules flagged people who visited torproject.org or searched for Tails
- Candidate: optional sentence for T1-22 (Tor)

#### A · Accessibility

**B-25 · Dark mode: no base rate.** Nielsen Norman Group (n = 115) found about a third each prefer dark, light or both ([NN/g](https://nngroup.com/articles/dark-mode-users-issues)); other figures come from unsourced aggregators. Confirms dark mode stays a non-inference. Contrast and forced colors: see A-30.

#### H · History

**B-26 · Where you came from hints at your age**
- Data: referrer domain (T1-29) · Groups: H · Inference: likely age range
- Source: [Pew, Americans' Social Media Use 2025](https://www.pewresearch.org/internet/2025/11/20/americans-social-media-use-2025/)
- Research · Strength (✓ 2026 fact sheet, ages 18–29 / 65+): Reddit 40% / 6%, TikTok 65% / 11%, Instagram 79% / 21%. Facebook (69–59%) and YouTube (95–65%) are used by a majority in all age groups
- Frame: "people who use [Reddit] skew young…" · How: medium · Needs: have (in-app browsers often strip the referrer)
- Candidate: clause on T1-29 for Reddit, TikTok and Instagram referrers. No under-18 statements (devlog 021)

**B-27 · Search-engine users: no reliable profile.** DuckDuckGo users are about 70% male per panel estimates ([MediaPost](https://www.mediapost.com/publications/article/208149/the-demographics-behind-duckduckgo.html), [Search Endurance](https://searchendurance.com/duckduckgo-statistics/)); age data conflicts. Candidate: none

#### New data
Nothing new beyond Pass A. B-16 would need text of known difficulty, and the B-09 check uses ASN data the page already has.

#### To check in primary sources before building (Pass B)
- The Markup's data (B-01): which ZIPs and providers, and whether it can be bundled
- Recon Analytics Starlink figures (B-03); Pew 2025 social-media table headers (B-26)
- A current Pew figure on Spanish dominance by generation (B-20)
- The current list of states that require honoring GPC (B-23)
- Whether prepaid brands have their own ASNs (B-09)

### Pass C · Combine (devlog 023)

**Method.** Attributes from Pass A × evidence groups, filled from A-xx, B-xx, C-xx and existing rules. An attribute with sourced, non-weak findings in **two or more independent groups** becomes a candidate Tier 2 conclusion (C-01 onward). Industry and bad-actor findings add *targeting sentences* to a conclusion but don't count toward its threshold. Nothing about minors; the teen guard on income stays (devlog 021).

**New sources found in this pass:**
- **Brokers' income guesses are often wrong.** Pew compared four voter-file vendors' modeled income with what people reported: only 30–46% were in the right income category ([Pew 2018](https://www.pewresearch.org/methods/2018/02/15/demographic-data/)). Profiles of lower-income people are sparser and less accurate ([IMC 2021](https://conferences.sigcomm.org/imc/2021/pdf/broker.pdf); [*Management Science* 2024](https://ideas.repec.org/a/inm/ormnsc/v70y2024i11p8003-8029.html))
- **Night work among nurses: ✗ withdrawn.** The cited page is a Korean nurses' study, not ACS data. No replacement found (one self-selected industry survey: about 11% of nurses work nights). C-05 has no base-rate source
- **Students and Macs.** 40% of college students use a Mac for school (Jamf / Vanson Bourne 2019, five countries, vendor-commissioned; [Jamf](https://www.jamf.com/resources/press-releases/new-research-finds-71-of-students-in-higher-education-today-use-or-would-prefer-to-use-mac/)). Too common to add anything to T2-H
- **Immigrants and phones bought abroad:** **no source found** (searched Pew and others). The T2-D timeframe stays unsupported at the step "phone bought abroad → moved with it"

#### Grid

Cells list the findings or rules linking each group to each attribute. **Bold** = counts toward a threshold. *Italic* = targeting sentence only (industry or bad-actor use). — = nothing found.

| Attribute | I · Connection | D · Device | T · Time | L/Z · Locale | W · Software | P · Privacy | A · Access. | H · History |
|---|---|---|---|---|---|---|---|---|
| Higher income | **ZIP income (ACS)**; B-04 guard | **A-01, B-07, T1-03/04/05**; A-15, B-08 guards | — | — | **T1-17** | — | — | — |
| Tight budget | **A-07, T1-32 library (B-06)**, ZIP income; *B-01, A-04* | **T1-07, T1-08 (B-11), T1-06 (B-10)** | — | — | — | — | — | — |
| How you'd be priced | *A-04, A-05, B-01* | *A-02, A-03, A-37, A-40* | *A-06, B-14* | *B-18* | — | — | — | — |
| Employer / at work | **T1-32 org**; *A-09, A-10* | **computer** | **weekday 9–5** | — | — | — | — | — |
| Industry | **network type (hospital, school, government, university)**; area industry (unsourced) | premium device (T2-J) | **work hours** | — | T1-17, T1-18 (unsourced) | — | — | — |
| Night shift | **hospital network** | **computer** | **midnight–5 AM (A-12, C-sources)** | — | — | — | — | — |
| Servicemember | **military network**; *A-11* | — | — | — | — | — | — | — |
| Student on campus | **university network** | Mac (too common) | **midnight–5 AM** | — | — | — | — | — |
| Rural | **satellite (B-03)**; USDA RUCA (same group) | — | — | — | — | — | — | — |
| Language community ⚑ | ZIP language share (ACS) | — | — | **T1-11 (A-22)** | **T1-13, T1-30** | — | — | — |
| Immigrant ⚑ | **in the US** | **international model (T2-D)**, release year (A-26) | — | **B-20** | **T1-13, T1-30** | — | — | — |
| Traveling | **connection country** | — | — | **language + clock agree** | — | — | — | — |
| Hiding from trackers | **T1-33**; *B-23 state law* | — | — | — | — | **T1-20 to 23**; *B-22, B-24* | — | — |
| Age range | — | (teen guard only) | — | — | — | — | — | B-26 |
| Disability ⚑ | — | — | — | — | T1-25 (unsourced) | — | **A-30** | — |
| Health ⚑ | *hospital (A-35)* | — | A-33 (too weak) | — | — | — | — | — |
| Politics, religion, sexuality, gender, parenthood, job loss | — | — | — | — | — | — | — | — |

#### Candidates

**C-01 · Income band** · extends T2-A (location path) · ⚑ finances
- Groups: D (premium device: A-01 iPhone, B-07 Mac, T1-03/04/05) + I (ZIP median income, ACS) (+ W, T1-17, optional third)
- Guards: no reading from an iPhone on a school network or during school hours (A-15); fiber isn't an income signal (B-04); Android isn't a low-income signal (B-08); premium device in a low-income ZIP → no conclusion, feed C-08 (likely industry) instead (as planned)
- Wording: "Your [MacBook Pro] and the median income where you live ([$X]) put you most likely in a household earning [$X–Y]." Add an honesty sentence (new source above): "Data brokers make this same guess about you, and they're wrong more often than right: in one test, their income estimates put only 30–46% of people in the right bracket." Optional targeting sentence on Macs (A-03)
- Confidence: Likely · Needs: ACS by ZIP; a defensible way to combine device and area (still a `?`)

**C-02 · Tight budget, with how it's used against you** · extends T2-B · ⚑ finances
- Groups: D (T1-07 low memory; data saver T1-08, a rare deliberate setting in the US, B-11) + I (mobile-only at home hours A-07, library network B-06, or a low-income ZIP)
- Targeting sentences: broker segments "Hard Times," "Credit Crunched: City Families" (A-08); if the provider is AT&T, Verizon, EarthLink or CenturyLink in a low-income ZIP, "you're likely paying the same as richer neighborhoods for slower internet" (B-01); location pricing (A-04)
- Caveat: being on cellular at home hours doesn't prove there's no home broadband (A-07), so that path needs a second I signal or the ZIP
- Confidence: Likely · Needs: ACS by ZIP

**C-03 · How you'd be priced right now** · **new** Tier 2 (targeting composite)
- Not a hidden fact but a sum of documented practices, each triggered by an observed signal. Fires when signals from **two or more groups** apply: D (Mac → pricier hotels shown first, A-03; phone → pricier products, A-37; battery under 20% and not charging → more willing to pay surge, A-40), I (ZIP-based prices, A-04), Z (clock vs. connection mismatch → fraud flag, B-18), T (mouse behavior used in pricing, A-06)
- Wording: "Sites that adjust prices would read you like this: [a Mac user, the kind Orbitz showed pricier hotels first]; [your battery is at 12%, which Uber found makes people more willing to pay surge prices]; [your clock and connection disagree, which fraud systems flag]."
- Confidence: Strong for "these practices exist", but never a claim that *this* site or any particular site is charging the visitor more
- Needs: have (ZIP part needs ACS)

**C-04 · At work at [employer]** · extends T2-G
- Groups: I (organization network) + D (computer) + T (weekday 9–5), as built
- New variants:
  - **Government network** (new provider type, A-10): "You're on a [state government] network during working hours. You most likely work in government, which makes you the audience lobbying and advocacy firms buy IP-targeted ads to reach."
  - **Any employer:** the sales-software sentence (A-09)
- Caution: IP location is less accurate on business networks (B-02); say "registered to", not "located at"
- Confidence: Likely

**C-05 · Night shift at the hospital** · **new** Tier 2 · ⚑ health (context)
- Groups: I (hospital network) + T (midnight–5 AM) + D (computer)
- Readings: on a hospital network at night, a **computer** suggests staff on a night shift (19% of RNs work evenings or nights; most RNs work in hospitals). A **phone** fits a patient, visitor or staff member equally, so make no conclusion on a phone
- Wording: "You're on [Mercy Hospital]'s network at [3:10 AM] on a computer. You're most likely working a night shift there."
- Targeting sentence: hospital websites have leaked visitors' appointment details to Facebook (A-35)
- Gap: no source that hospital computers at night are mostly staff-operated. Logically strong, but unsourced
- Confidence: Likely

**C-06 · Likely industry from the network** · replaces T2-J's area path as the main route
- Groups: I (network type) + T (working hours) (+ D computer)
- Hospital → healthcare; school district → education; university → higher education; government → public sector; military → armed forces. Hospital, school and university are already in the provider table; government is new (C-04). Stronger than the area-based path, which has no person-level source
- Wording: "On [Lincoln Unified School District]'s network on a weekday morning, on a computer: you most likely work in education."
- Ethics: school networks are mostly used by students, including minors. Fire only on a **computer during staff hours**, and never conclude anything about a student (devlog 021). Needs the user's review
- Area path (premium device in a low-income ZIP → best-paid local industry) stays Planned and unsourced
- Confidence: Likely (network + hours)

**C-07 · Recent immigrant** · extends T2-D · ⚑⚑ immigration
- Groups: L (Spanish or another language first; B-20: 61% of Spanish-dominant Hispanic adults are immigrants) + D (international model code) + I (connecting from the US) (+ W, local voices or keyboard)
- Timeframe: phone release year (T1-01) + replacement cycle about 2.5 years (A-26) → "most likely within the last [3] years". Weak link: **no source** that immigrants bring their phones with them (searched). Keep alternative readings in the text (visitor, relative's phone, device bought abroad)
- Targeting sentences: immigration enforcement buys ad-tech and location data (A-25, "this is the kind of data"); impersonation scams aimed at Spanish speakers (A-23)
- Confidence: Likely (immigrant) / Weak (timeframe; must not count as Strong)
- Needs: the user's call on wording; current Pew figures

**C-08 · Language community** · extends T2-C · ⚑
- Groups: L (T1-11) + W (T1-13, T1-30) (+ I, ZIP language share)
- `[STAT]`: about 92% of people who speak Spanish at home are Hispanic or Latino (A-22, 2011 data)
- Targeting sentences: Facebook labeled ethnicity from behavior and let advertisers exclude by it from housing ads (A-24); geographic pricing has charged Asian customers more (A-05, only when the language is Chinese, Korean, Vietnamese, etc. and the ZIP fires)
- Confidence: Likely

**C-09 · Traveling** · extends T2-I
- Groups: L + Z (agree) + I (another country), as built
- Targeting sentences: hotel sites change prices by where you connect from (A-37); the mismatch is a fraud flag (B-18)
- Confidence: Likely

**C-10 · Hiding, and what it costs** · extends T2-F
- Groups: P (two privacy signals, one blocking) (+ I, the visitor's state)
- Additions: DNT is ignored and makes the browser easier to single out (B-22); in states that require it, sites must honor GPC (B-23); a VPN alone is common (B-05), so the two-signal rule stays; Tor and privacy-tool searches have drawn surveillance attention (B-24, optional)
- Confidence: Strong (observed)

**Single-group, stays Tier 1 (with targeting sentences):** servicemember (I, A-11); rural via satellite (I, B-03, with RUCA in the same group); age range from referrer (H, B-26). Student on campus (T2-H) is unchanged.

**No candidate:** disability (A-30 is one group; T1-25 is unsourced, so T2-E stays at risk); health beyond hospital context; politics, religion, sexuality, gender, parenthood, job loss.

#### To check (Pass C)
- Pew 2018 voter-file income accuracy figures (C-01)
- A current source on night work among hospital staff (C-05)
- Any source on immigrants keeping phones bought abroad (C-07)
- A way to combine device and ZIP income into a band (C-01): the method, not just the inputs

### Pass D · The advertiser's view and risky combinations (devlog 024)

Same format and caveats as earlier passes (figures come from search summaries). Part 1 (M-xx) weighs industry sources: what advertisers and marketers *think* and do, accurate or not. Part 2 (R-xx) looks for combinations that put someone at risk. Risk wording describes the danger, never how to exploit it. Nothing about minors.

**Strongest:** M-02, M-03, M-05, M-10 (advertiser view); R-01, R-02, R-03, R-07, R-09 (risk).

#### Part 1 · How advertisers would read this visitor

**M-01 · Everything this page sees goes out in ad auctions**
- Data: user agent, device make and model, OS, language, IP, location, connection type, carrier · Groups: D, L, I
- Source: IAB OpenRTB 2.6 device object ([IAB Tech Lab](https://github.com/InteractiveAdvertisingBureau/openrtb2.x); [Google's OpenRTB spec](https://developers.google.com/authorized-buyers/rtb/downloads/openrtb-proto)); [ICCL 2023](https://iccl.ie/?p=13455)
- Industry standard (✓ spec read) · The OpenRTB 2.6 device object includes user agent, IP, device type, make, model, OS and version, screen size and pixel ratio, language, carrier, connection type, advertising ID, Do Not Track, and a geo object (coordinates, country, region, city, ZIP, UTC offset)
- How: medium · Candidate: framing sentence for the profile: "When a site shows ads, much of what's listed above is sent to the companies bidding to show you one."

**M-02 · Advertisers pay more to reach iPhone users**
- Data: iOS (T1-02) · Groups: D
- Source: [Appodeal Q4 2024](https://appodeal.com/the-mobile-ecpm-report-updated-q4-2024-view); [Singular quarterly reports](https://www.singular.net/quarterly-report-2025-q2/); PropellerAds (Aug 2026, via search; network-wide figure)
- Industry data · Rel.: shown, volatile · Strength: "iOS consistently had higher eCPM than Android" (Appodeal, ✓). The 1.75× figure (PropellerAds) is unverified; don't quote a number until checked. The gap swings quarter to quarter
- Frame: "advertisers pay…" · How: high · Needs: have
- Candidate: targeting sentence on T1-02 ("Advertisers pay more to show you an ad than they'd pay for an Android user.")

**M-03 · Your ZIP code is an income tier in Google Ads**
- Data: ZIP (T1-31) · Groups: I · Inference: advertisers can bid up for you or leave you out based on your area's income
- Source: [Google Ads demographic targeting](https://support.google.com/google-ads/answer/2580383); [Digital Position](https://digitalposition.com/resources/blog/ppc/how-does-google-ads-determine-household-income-hhi-targeting/)
- Industry practice · Rel.: shown (documented feature) · Google groups US areas by IRS income data into tiers (top 10%, 11–20% … lower 50%). Advertisers can bid more or exclude tiers; some sources say it now uses more than the ZIP. ZIP targeting is barred for housing, employment and credit ads, but income tiers are not listed as restricted
- Frame: "in Google's ad system, your area…" · How: high · Needs: IRS income by ZIP (public; bundle) or ACS
- Candidate: sentence attached to T1-31 or C-01: "In Google Ads, [your area] falls in the [top 10%] household-income tier. Advertisers can pay more to reach you there, or leave you out."

**M-04 · 650,000 labels**
- Source: [The Markup 2023](https://themarkup.org/privacy/2023/06/08/from-heavy-purchasers-of-pregnancy-tests-to-the-depression-prone-we-found-650000-ways-advertisers-label-you); [Cracked Labs, "The Xandr File"](https://wolfie.crackedlabs.org/en/xandrfile)
- Industry (published by Microsoft's ad platform itself) · Segments included "heavy purchasers" of pregnancy tests, depression-related categories, consumer credit and military groups
- How: medium · Candidate: framing for the profile or explanatory text

**M-05 · Marketers rank Hispanic Americans by "acculturation"**
- Data: language and its order (T1-11), W · Groups: L, W · Inference: the industry has a ready-made tier for you
- Source: Claritas Hispanicity release notes ([2025](https://claritas360.claritas.com/knowledgecenter/help/content/claritas%20360/news_and_info/2025/2025/claritas%20culturecode%20hispanicity%20distributions%202025%20release%20notes.pdf)); [Lotame Hispanic segments](https://resources.lotame.com/hubfs/2020%20LDX%20Marketing/LDX_HispanicSegments_FINAL2020.pdf); [Acxiom Personicx Hispanic](https://www.acxiom.com/wp-content/uploads/2020/07/ac-1079-19-fs-personicx-hispanic.pdf)
- Industry · Rel.: unproven for an individual. Lotame (✓) sells segments by language (Spanish, English, Portuguese, Bilingual), acculturation (1st, 2nd, 3rd generation) and country of origin (20 countries). Claritas's HA1–HA5 acculturation codes and Acxiom's 55 segments come from search summaries only (pages blocked or not reached)
- Frame: "marketers sort…" · How: high · ⚑ ethnicity, immigration · Needs: have
- Candidate: targeting sentence in C-08/C-07: "Marketers sort Hispanic Americans into tiers from 'English dominant, third generation or later' to 'unacculturated', and sell segments by generation and country of origin. A browser set to Spanish first is the kind of signal those labels are built on."

**M-06 · Your employer's "intent" is sold to vendors**
- Data: organization network (T1-32) · Groups: I (+ T)
- Source: [Bombora Company Surge](https://bombora.com/intent/); [Bombora on sales use](https://bombora.com/blog/how-bomboras-intent-data-fuels-b2b-sales-activities/)
- Industry · Rel.: shown (product) · A co-op of "thousands of media destinations" tracks what each company reads and flags "surges" (the last 3 weeks vs. a 12-week baseline) across 25,300+ topics. Sold to sales teams (third-party guide: about $25,000/year). How visits are tied to companies isn't documented in what I found (likely IP, as in A-09)
- How: medium–high · Candidate: sentence in T2-G with A-09: "Data co-ops track what each company's staff read online and sell 'surges' in interest to vendors."

**M-07 · Lenders aim at the financially stretched**
- Source: [Media Matters 2022 (TikTok)](https://www.mediamatters.org/tiktok/tiktok-letting-payday-lenders-prey-financially-vulnerable-users); [Upturn, "Led Astray," 2015](https://upturn.org/static/reports/2015/led-astray/files/Upturn_-_Led_Astray_v.1.0.pdf); [Pew Trusts 2014](https://www.pewtrusts.org/-/media/assets/2014/10/payday-lending-report/fraud_and_abuse_online_harmful_practices_in_internet_payday_lending.pdf)
- Industry / bad-actor · Cash-advance ads targeted people living "paycheck to paycheck"; online lead generators sold borrowers' applications on, including into states where payday loans are illegal; 30% of online payday borrowers report threats. **Late-night ad timing: anecdotal only**
- Candidate: sentence in C-02 alongside A-08

**Cross-references (Pass A advertiser findings):** Mac users shown pricier hotels (A-03); battery and surge pricing (A-40); employer lookup (A-09); lobbying audiences (A-10); "ethnic affinity" (A-24); browser-based hiring scores (A-14); brokers' income guesses often wrong (Pass C sources).

**M-10 · Your ad profile** · **new** composite (parallel to C-03)
- Assembles the labels an advertiser would attach, each from a fired signal with a source: price tier from the device (M-02, A-01, B-07), income tier from the ZIP (M-03), language or acculturation segment (M-05), employer and intent if on an organization network (A-09, M-06), "hard to reach" if privacy tools fire (T2-F). Fires when **two or more groups** contribute
- Wording: "Here's roughly how an ad system would file you: [iPhone user: costs more to reach] · [top-10% income area] · [Spanish-language segment] · [on Acme Corp's network]. Brokers' versions of these labels are often wrong (in one test, their income guesses were right 30–46% of the time), but they're still used."
- Confidence: Strong that the practices exist; never claims a specific company has filed *this* visitor
- Needs: IRS or ACS income by ZIP

#### Part 2 · Combinations that put someone at risk

**R-01 · Narrowed to a handful of people**
- Combination: organization network (I) + city or ZIP (I) + device model (D) + language (L), especially an uncommon language or device · Exploited by: anyone holding this visit record plus one other dataset (staff directory, social profiles)
- Source: [Rocher, Hendrickx & de Montjoye, *Nature Communications* 2019](https://pmc.ncbi.nlm.nih.gov/articles/PMC6650473) (15 attributes re-identify 99.98% of Americans, modeled); [Sweeney 2000](https://dataprivacylab.org/projects/identifiability/paper1.pdf) (ZIP + birth date + sex: up to 87%; Golle's later estimate: 63%); [EFF Panopticlick](https://coveryourtracks.eff.org/about) (browser fingerprints: at best 1 in 286,777 share one)
- Research · How often it fires: whenever an organization network is detected; strongest with an unusual device or language
- Wording: "Your employer's network, your city and a [Pixel 8 set to Korean] together narrow you to very few people. Matching this against a staff directory could be enough to name you." The page can't count the crowd, so no number is given
- How: high · Needs: have · Caveat: re-identification figures use demographics like birth date, not these signals. The principle carries over, but no source measures this exact combination

**R-02 · Your employer can see this visit**
- Combination: organization network (I) + working hours (T), i.e. T2-G firing · Exploited by: the employer
- Source: ExpressVPN survey of 1,500 US employers, Sept 2024, via [Jibble](https://www.jibble.io/news/employee-online-monitoring) (original page gone): 74% use online monitoring and 74% log web browsing (vendor-commissioned)
- Industry survey · Rel.: shown, but the vendor has an interest
- Wording (in T2-G): "About three in four US employers log the websites staff visit. [Acme] can most likely see you came here, and when."
- How: high · Needs: have

**R-03 · Officials and servicemembers: a security target**
- Combination: military or government network (I) + device (D) + location (I) · Exploited by: foreign intelligence services, blackmailers
- Source: [ICCL, "America's hidden security crisis," 2023](https://iccl.ie/?p=13455) (✓ "U.S. military personnel and political decision makers are targeted using RTB"; RTB data sent to Russia and China; "Patternz" uses RTB to profile 5 billion people. Judges and live location weren't on the summary page); [Duke 2023](https://techpolicy.sanford.duke.edu/wp-content/uploads/sites/4/2023/11/Sherman-et-al-2023-Data-Brokers-and-the-Sale-of-Data-on-US-Military-Personnel.pdf) (A-11); [The Register](https://www.theregister.com/2023/11/16/online_ad_auction_data_risks/). Google disputes the ICCL's claims
- Research / investigation · Wording (on military and government networks): "Researchers found ad-auction data used to target US military personnel and political decision makers, and sent to firms in Russia and China."
- How: high · Needs: have (government type from C-04)

**R-04 · Reproductive health: recorded, not to be inferred**
- Combination that would matter: a clinic or hospital network, or a connection in a different state from home, in a state with an abortion ban · Exploited by: anti-abortion advertisers, prosecutors
- Source: [Vice 2022, SafeGraph](https://www.vice.com/en/article/location-data-abortion-clinics-safegraph-planned-parenthood/) (a week of visitor data for 600+ Planned Parenthood sites cost about $160); [Massachusetts AG vs. Copley, 2017](https://www.thedrum.com/news/2017/04/04/advertiser-barred-geo-targeting-anti-abortion-ads-landmark-ruling) (anti-abortion ads geofenced around clinics); [Guttmacher 2024](https://www.guttmacher.org/news-release/2025/guttmacher-institute-releases-full-year-us-abortion-data-2024) (155,000 people crossed state lines, 15% of abortions in states without total bans); [Pregnancy Justice 2025](https://www.pregnancyjusticeus.org/wp-content/uploads/2025/09/Pregnancy-as-a-Crime-An-Interim-Update-on-the-First-Two-Years-After-Dobbs.pdf) (at least 412 pregnancy-related prosecutions in two years after Dobbs; the role of digital evidence wasn't confirmed)
- Bad-actor use · ⚑⚑ health
- Candidate: **no person-level inference.** The page can't identify clinic networks, and a time zone can't tell which state is home. Possible explanatory content only (what location data has been used for). The user decides

**R-05 · Immigrant + location** (strengthens C-07)
- Combination: Spanish-first browser (L) + international model phone (D) + US city or ZIP (I) · Exploited by: immigration enforcement (buys ad-tech and location data, A-25); impersonation scammers (A-23); marketers' acculturation tiers (M-05)
- Wording stays at "this is the kind of data…" · ⚑⚑ · Needs the user's call (as C-07)

**R-06 · Lists of vulnerable people sold to scammers**
- Combination: tight budget (C-02) or Spanish-language community (C-08) · Exploited by: fraud rings
- Source: [US DOJ 2021, Epsilon](https://www.justice.gov/opa/pr/marketing-company-agrees-pay-150-million-facilitating-elder-fraud-schemes): Epsilon paid $150M after its unit sold data on 30M+ consumers to fake sweepstakes and astrology schemes (2008–2017), which hit the elderly and vulnerable hardest; two employees were sentenced
- Enforcement · Wording: "Data brokers have sold lists of people in exactly this situation directly to scammers; one paid $150 million to settle charges." Age can't be inferred here (A-19), so no elderly-specific statement
- How: medium–high

**R-07 · Anyone who gets you to open a link learns this much**
- Combination: home internet provider (I) + city or ZIP (I) (+ late-night visit, T) · Exploited by: stalkers, abusive partners, harassers
- Source: IP accuracy on home connections (B-02: median 2.62 km with paid data; free data like this page's is less precise); [NNEDV Safety Net](https://nnedv.org/content/technology-safety/) (tech abuse in domestic violence; the "97%" figure isn't on this page and is dropped); VPN as protection ([UCI DV clinic safety plan](https://www.law.uci.edu/academics/real-life-learning/clinics/dvc/pdfs/Technology-Abuse-Safety-Plan.pdf))
- Research + advocacy · How it works (no exploit detail): any site or link a person opens receives their IP address, which is how this page knows the visitor's area
- Wording (on T1-31, home networks only): "Any site or link you open learns this much. On a home connection, paid location services can often place you within a few miles of where you live, which is why domestic-violence advocates recommend a VPN for people at risk."
- How: high · Needs: have

**R-08 · Hiding makes you stand out** (cross-reference): privacy tools draw fraud flags (B-18), make the browser more distinctive (B-22) and have drawn surveillance attention (B-24). Already in C-10.

**R-09 · Servicemember under financial strain**
- Combination: military network (I) + tight-budget signals (D), i.e. T1-32 military + T2-B · Exploited by: predatory lenders near bases (A-11), broker lists (A-08, R-06)
- Source: A-11 (DoD 2006: servicemembers disproportionately targeted by predatory lenders; brokers sold servicemember net worth). Security clearances: about half of DOHA clearance denials in a 2007 sample involved "Financial Considerations," the top issue ([ClearanceJobs](https://news.clearancejobs.com/2020/09/01/the-impact-of-delinquent-debt-on-security-clearances/), secondary; no current official figure)
- Wording: "On a military network, with a phone and settings that suggest you're watching costs: this is the profile predatory lenders near bases have targeted."
- How: high · Needs: have · Fires rarely

**R-10 · Patient on a hospital network**
- Combination: hospital network (I) + phone (D) (+ night, T) · Exploited by: ad platforms receiving health data (A-35), health-segment buyers (M-04, A-34)
- No conclusion (C-05 holds: patient, visitor or staff fit equally on a phone). Sentence only: "Hospital websites have leaked visitors' appointment details to Facebook, and ad platforms have sold health-related audience labels."

**No risk inference possible:** sexuality, religion and politics. No signal here supports them (A-27 to A-29). Recorded so they aren't proposed.

#### To check (Pass D)
- OpenRTB 2.6 device-object field list (M-01)
- Google Ads household-income method today (M-03)
- NNEDV "97%" figure (R-07)
- Security clearance denials and financial considerations (R-09)
- ExpressVPN survey method (R-02)

### Source check (devlog 025)

Every finding with a candidate was checked against its source: pages read directly, PDFs extracted and searched in full. "Candidate: none" items were skipped, as agreed. Wrong figures are corrected in place above; this table records the verdicts.

**Key:** ✓ verified · ✎ corrected in place · ~ partly verified (the claim holds; a detail is unconfirmed) · ✗ unverifiable (source blocked, gone or paywalled; **check again before building**)

| Verdict | Findings |
|---|---|
| ✓ | A-01, A-02, A-06, A-08, A-12, A-15, A-18, A-22, A-24, A-26, A-30, A-34, A-35, A-37, A-41, B-06, B-14, B-20, B-22, M-01, M-04, M-07, R-01, Pass C (Pew voter files, Jamf) |
| ✎ | A-05, A-07, A-09, A-11, A-32, A-36, A-39, B-01, B-02, B-03, B-05, B-10, B-11, B-26, M-02, M-05, M-06, R-02, R-03, R-04, R-07, C-05 base rate (withdrawn) |
| ~ | A-03, A-04, A-14, A-23, A-25, B-07, B-08, B-18, B-19, B-23, B-24, M-03, R-09 |
| ✗ | A-10, A-40, B-04, R-06; parts of A-04, A-25, B-07, B-08, M-05, R-04 (listed below) |

**Corrections that change what we'd say:**
- **A-08 is stronger:** all the segment names the Senate report was said to contain are confirmed, including "Rural and Barely Making It" and "Ethnic Second-City Strugglers"
- **A-02 is richer:** late-night orders and arriving via an ad were also lending risk signals (links to T1-16 and T1-29)
- **A-32 had the wrong citation:** corrected to White, Doraiswamy & Horvitz 2018 and White & Horvitz 2019
- **Location precision (B-02, R-07):** the 2.6 km figure used paid location data; this page uses free data, so the R-07 wording is softened
- **Newer figures:** smartphone-only by income (A-07: 29% under $30K in 2026), Windows 10 share (B-10: about 15%), social media by age (B-26: Reddit 40% of 18–29)
- **Dropped figures:** NNEDV "97%" (R-07), Save-Data 28% (B-11), Abmatic 1.2M sessions (A-09), Porch $8,068 (A-36), Hunch degree figures (B-07), the "1.75×" iOS premium (M-02, until checked), nurses' night work (C-05: no base rate)
- **Narrowed claims:** Princeton Review pricing is by area, not per customer (A-05); ICCL's report names military personnel and political decision makers, not judges (R-03); Guttmacher's 15% is of abortions in states without bans (R-04); 74% of employers log browsing, not 62% (R-02)

**Still unverified (check before building anything that uses them):**
- **A-10 lobbying audiences:** Rational 360 page gone (404), FiscalNote blocked (403). The user's own example; needs a working source
- **A-40 Uber battery:** Quartz blocked, TechRadar unreadable. Widely reported, but not read first-hand
- **R-06 Epsilon:** both DOJ pages returned empty. Search summaries of the DOJ release agree on $150M and 30M+ consumers
- **A-04 Staples figures:** the direction is confirmed (WTOP), but 42,000 ZIPs, the 90% figure and $59,900 vs. $48,700 are only in the paywalled WSJ original
- **A-25:** ICE's January 2026 request is confirmed (The Register); the 2025 PenLink purchase is not (Axios blocked)
- **B-04 fiber income** (Fierce blocked); **B-07** Hunch figures; **B-08** "Android is flat across incomes" (not on the Pew page)
- **M-03:** Google's tiers and bid controls are confirmed; the IRS data basis comes from secondary sources only
- **M-05:** Claritas codes and Acxiom's 55 segments (pages blocked); Lotame is confirmed
- **R-04:** Copley settlement (Mass.gov blocked); Pregnancy Justice figures (not fetched)
- **B-18:** Microsoft's time-zone-mismatch attribute (link landed on the wrong page); Fingerprint and SEON are confirmed

## 10. Process

- Run Pass A, then Pass B, then Pass C (§5); log every finding in §9
- The user reviews findings; nothing is built without sign-off
- The third-party rule applies only to services or new data points that would receive visitor data. Reference sources and locally bundled datasets don't receive any, so any source can be used; reliability decides the wording, not inclusion (§4)
- Afterwards: update test visitors (incl. IP data) and run them
