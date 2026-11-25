# Research Plan

The plan for "Next up" step 3 (`CLAUDE.md`). Set up in devlog 019; research has not started.

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

_None yet._

## 10. Process

- Run Pass A, then Pass B, then Pass C (§5); log every finding in §9
- The user reviews findings; nothing is built without sign-off
- Every data source and dataset is checked against the third-party rule
- Afterwards: update test visitors (incl. IP data) and run them
