# Inference Map

**Status:** designed without new research (devlog 008), built into `index.html` (devlogs 010, 017, 018) and shown in the profile. Research folded in (devlog 026) from `research-plan.md` §9 (Passes A–D, source-checked in devlog 025). Finding IDs (A-01, B-18, C-05, M-03, R-07…) point there. Research items built in devlog 028; statuses brought up to date in devlog 031. Anything still marked **Planned** is designed and not yet built.

**Standards**
- Reflect back what we *know* about the visitor. If something can't be inferred, say nothing. The exception is **conspicuous absence**: privacy tools that hide or fake data.
- Accuracy over alarm. Where it's a likelihood, the statement says so.
- Confidence and sources are tracked here for review. Display is decided later (sources likely at the bottom of the page).
- Gender and sexuality are absent on purpose: no collected signal supports a truthful inference.
- **Aim for "how did it know that?"** (devlog 018). A statement should reveal something hidden about *this person*. Common knowledge (e.g. sunset times) and facts about an area (e.g. a ZIP code's median income) don't produce that moment, so they are never statements on their own.
- **Area data is an input, not a statement.** Census or industry figures for a ZIP code or county only feed conclusions about the person: income band, rent, home ownership, likely industry. Target boldness, from the user: "Based on your high-end hardware and the median income of your location, it's likely you make between X and Y per year. You pay X in rent per month and are Y% likely to own your home."
- **Targeting statements belong in the profile** when they are truthful, considered and impactful (devlog 019; replaces Part 1, problem 8 for these). How advertisers, marketers or bad actors would characterize or target the visitor counts, even when their inference is unreliable. See `research-plan.md` §4.
- **Network type and employer are high-value.** What kind of network someone is on (workplace, campus, hospital, library, prison) says a lot about who they are and where they are right now.
- **Targeting statements sit inside the rule they attach to** (devlog 026), marked **Targeting**. The observed fact comes first; the targeting sentence says what it means to someone selling to, pricing, flagging or exploiting the visitor.
- **Risk statements** (R-xx) describe a danger, never how to exploit it.
- **Nothing about minors** (devlog 021): no rule collects or reflects anything that singles out under-18s. Allowed: naming a school-district network; flagging a likely teen only to withhold an income reading.

**Keys**
- **Confidence (provisional):** **Obs** = observed fact · **Strong** = well documented, few exceptions · **Likely** = real correlation, many exceptions · **Weak** = pattern used by industry, often wrong
- **Source:** ✓ = source checked (devlog 025) · **~** = partly verified: the claim holds, a detail is unconfirmed · **✗ source needed** = the source couldn't be read; find a working one before building · **?** = no source found · *n/a* = observed, no source needed
- **⚑ Needs decision** = the user decides whether and how this is said before it's built
- **⚑** = sensitive (health, disability, finances, origin)

---

## Part 1: Review of the existing engine (`buildProfile()`)

**Implemented in devlog 009.** Cuts removed; corrections rewritten as observed statements, with no `[STAT]` clauses. The UK GDPR line was removed from the profile. The summary row now shows the time-zone name, labels randomized fingerprints, and no longer shows Local IP. `renderCases()` is unchanged.

### Problems that affect many claims
1. **Time zones are not cities.** *(Addressed by T1-31 when the IP lookup works, devlog 017.)* Browsers return IANA zone names like `America/New_York` and `America/Chicago`. `America/New_York` covers the entire US Eastern zone, so anyone in Miami, Atlanta or DC is told "your timezone places you in New York." Most `tzMap` keys (`San_Francisco`, `Seattle`, `Austin`, `Dallas`, `Boston`…) are not IANA zone names, so they never match. ✓ IANA tz database
2. **The WebRTC local IP is almost never available.** Chrome, Safari and Firefox now hide local addresses behind random `.local` names, and with `iceServers:[]` there is no public address either. Every branch that depends on `localIP` is effectively dead. ✓ WebRTC mDNS candidate obfuscation (Chrome 2019+, others since)
3. **"10.x means a corporate network" is wrong.** Many home routers use 10.x, for example Comcast/Xfinity gateways (10.0.0.1). ✓
4. **The font flags misfire.** Futura, Avenir and Gill Sans come preinstalled on macOS, so `hasDesignFonts` is true for every Mac. Franklin Gothic Medium ships with Windows, so `hasOfficeFonts` is true for nearly every Windows PC. ✓ Apple and Microsoft font lists
5. **`navigator.connection.downlink` is capped at 10 Mbps in Chrome,** so the `downlink>20` ("fiber") branch never fires. ✓ Network Information API / Chrome implementation
6. **Camera and mic counts are limited before permission.** Before the visitor grants access, browsers report at most one device of each kind, so `cameras>1 && mics>1` almost never fires. ✓ W3C Media Capture spec
7. **`canvas === 'blocked'` rarely happens.** Privacy browsers randomize canvas output rather than throwing errors. The real signal is `S.canvasStable === false` (added in 004).
8. **Several narratives describe how data is used,** for example sales to insurers and law enforcement access. That belongs in Part Three, not in the reflection. *(Revised in devlog 019: targeting statements that are truthful, considered and impactful now belong in the profile. Generic usage narratives still go to Part Three.)*

### Claim by claim

| Section | Claim (paraphrased) | Verdict | Reason / replacement |
|---|---|---|---|
| Location | "Your timezone places you in [city]" | **Correct** | Region only: "Your clock is set to US Eastern time." → T1-10 |
| Location | NYC: high-end Mac is "a neighborhood-level signal" | **Cut** | Unsourced; location wrong (problem 1) |
| Location | SF/LA: "the region that built this infrastructure"; dev fonts → "the people who built these systems" | **Cut** | Branch can't match correctly; editorial |
| Location | Texas branch: "no comprehensive state data privacy law" | **Cut** | Dead code (problem 1), and **false**: Texas Data Privacy and Security Act took effect July 1, 2024 ✓ |
| Location | London: UK GDPR is real legislation | **Keep (move)** | True, but about law, not the visitor; better in Part Two/Three |
| Location | London: multilingual "correlates with immigrant background" | **Correct** | → T1-11/T1-12 (observed languages), T2-C (threshold) |
| Location | EU: "strongest data rights of anyone on earth" / "legitimate interest" | **Cut** | Opinion; not a reflection |
| Location | US + >2 languages "correlates strongly with immigrant communities" | **Correct** | "Strongly" unsourced. `en-US, en` already counts as 2. → T1-11, T2-C |
| Location | 10.x → corporate network, employer logging | **Cut** | Problems 2 and 3 |
| Location | Fallback: "a regional market… individual-level targeting" | **Cut** | Says nothing about the visitor |
| SES | Premium Mac + P3 + Adobe/dev fonts → "top household income bracket with high confidence" | **Correct** | Overstated; → T2-A with explicit threshold and `[STAT]` |
| SES | Adobe CC "approximately $60/month" | **Correct** | Price changed; → T1-17 with `[STAT: current Adobe CC price]` ✓ Adobe pricing page |
| SES | P3 is "almost exclusively Apple devices after 2016 and pro monitors" | **Cut** | **False**: many Android flagships and Windows laptops have P3 displays → T1-05 |
| SES | Connection >20 Mbps → fiber broadband | **Cut** | Dead code (problem 5) |
| SES | Save-Data → limited data plan, used by vendors as an SES signal | **Correct** | The setting is observed; the "used by vendors" part is usage → T1-08 |
| SES | Mac → income "in every major demographic study since 2010" | **Cut** | Unsourced, overstated. The iPhone has a real source → T1-02 |
| SES | Fallback: "median hardware tier… most extensively profiled" | **Cut** | Unsourced; nothing about the visitor |
| Profession | Linux + dev fonts → "almost certainly software engineering" | **Correct** | → T1-18 (Likely) |
| Profession | Dev fonts "exist because you put them there" | **Keep** | Basically true for JetBrains Mono, Fira Code, Hack, Inconsolata. Exclude Cascadia Code (ships with Windows Terminal) ? |
| Profession | Adobe + design fonts → "almost certainly a creative field" | **Correct** | Problem 4; use Adobe-only fonts → T1-17 (Likely) |
| Profession | Multiple cameras and mics → content creator | **Cut** | Problem 6 |
| Profession | Dev tools open → "almost exclusively developers" | **Cut** | Detection is a window-size guess; sidebars and zoom trigger it |
| Profession | Windows + Office fonts + work hours → white-collar | **Correct** | Problem 4; → T1-15 (observed time, Weak) |
| Profession | LinkedIn referrer → professional interest | **Correct** | Observed referrer only → T1-29 |
| Profession | Fallback | **Cut** | Says nothing |
| Fingerprint | Blocked: "fewer than 5% of users" | **Cut** | Unsourced |
| Fingerprint | TLS/HTTP2 fingerprinting "no consumer tool blocks" | **Cut** | Overstated; mostly used for bot detection, not ad tracking. Not a reflection |
| Fingerprint | Ad blocker: "top few percent by privacy awareness" | **Cut** | Unsourced |
| Fingerprint | "Did not prevent capturing… WebGL renderer, local IP" | **Correct** | WebGL wasn't collected before 003; local IP usually not available. → T1-20, T1-21, T2-F |
| Fingerprint | Default: "persists across incognito, cookie clearing, restarts" | **Correct** | Browser-dependent (see 004). Say only what was observed |
| Network | Local IP via WebRTC, subnet type | **Cut** | Problems 2 and 3 |
| Network | WebRTC used to correlate across VPNs | **Cut** | Largely mitigated; usage, not reflection |
| Network | Connection type gives "ISP identification" | **Cut** | **False**: connection type doesn't reveal the provider |
| Behavior | Battery <20% → "urgency, reduced purchase deliberation" | **Cut** | Rests on an anecdote (Uber, 2016) the company said it didn't act on. Battery level itself can be observed. *Revived as an attributed targeting statement, T1-37 (devlog 026)* |
| Behavior | Work-hours desktop → salaried, schedule autonomy, B2B premium | **Correct** | → T1-15 (observed + Weak) |
| Behavior | Late night → sold to insurers as an anxiety proxy | **Cut** | Usage + unsourced → T1-16 (observed time only) |
| Behavior | Mobile + not charging → "in transit" | **Cut** | Weak; phones are usually unplugged |
| Behavior | Weekend → persuadable, high news consumption | **Cut** | Unsourced |
| Behavior | Fallback | **Cut** | Says nothing |
| Political | Privacy score ≥3 → civil-libertarian/progressive "in voter file models" | **Cut** | Unsourced; counts Linux as a privacy tool. Revisit in research |
| Political | "Southern US" → warrantless data sales | **Cut** | `isSouthernUS` matches all of `America/Chicago` (incl. Minneapolis, Chicago) and Phoenix; usage claim |
| Political | Multilingual US → disinformation targeting | **Cut** | Usage → Part Three |
| Political | EU multilingual → distinct campaign segments | **Cut** | Unsourced |
| Political | Ad blocker → center-left "in voter file models" | **Cut** | Unsourced. Revisit in research |
| Political | Fallback | **Cut** | Says nothing |
| Referrer | None: "coincidence or habit" | **Correct** | Keep the observation, drop the editorial |
| Referrer | Google: query logged, "retained indefinitely" | **Correct** | Observed: the search terms weren't passed to this page. "Indefinitely" is false (retention settings exist) |
| Referrer | LinkedIn/Facebook: click attributed to your profile | **Correct** | Plausible (link wrappers) but ? and usage. Observed referrer only |
| Referrer | Other: "It has been logged and will be used" | **Correct** | Observed referrer only |

**Also affected:** `renderCases()` scores Part Three's case studies using `isUStz`, `isSouthernUS` and `isEUtz`, so it inherits problem 1. Out of scope here; flagged for the Part Three pass.

---

## Part 2: Tier 1 rules

Each rule: **trigger** (signals) → **statement** (draft wording, as it might read on the page). Each rule belongs to one **evidence group**, which Tier 2 uses to count independent signals.

**Evidence groups:** **D** device hardware · **W** installed software (fonts, voices) · **L** locale settings (language, region, clock format) · **Z** time zone · **T** time and behavior · **P** privacy tools · **A** accessibility settings · **H** visit history and referrer · **I** internet connection (IP lookup, recorder `N`; independent of the device's own settings)

### Device and spending

**T1-01 · Phone model** · D · Obs (model) / Strong (year)
- Trigger: `X.clientHints.model` (Android, Chromium only)
- "Your phone is a [Samsung Galaxy A15], released in [2023]."
- Source: *n/a* for the model; ? needs a model → release-year table
- **Dropped (devlog 037):** device age. With release years, "you've most likely had this phone for about [N] years." US consumers replace phones about every 2.5 years (Statista forecast for 2025: 2.49 years; ✓ A-26), and a quarter of likely buyers now expect to replace within 1–2 years (YouGov, Nov 2025). So a release year bounds when the phone was bought. Feeds T2-D's timeframe
- **Targeting (Built in T2-L, devlog 028):** "Some shopping and travel sites have been found to show different products to people on phones than to people on computers." ✓ Hannak et al., IMC 2014 (Home Depot and Travelocity) (A-37). Appears as T2-L's phone label, not on T1-01

**T1-02 · iPhone** · D · Likely
- Trigger: `S.isIOS` (phone screen)
- "You're on an iPhone. In the US, owning an iPhone was the single best predictor of high household income in 2016: it identified high earners correctly 69% of the time."
- Source: ✓ Bertrand & Kamenica, "Coming Apart?", *AEJ: Applied* 2023 (NBER WP 24771), via Chicago Booth Review (A-01)
- **Guard (Built, devlog 028, A-15):** 88% of US teens own an iPhone (Piper Sandler, spring 2025 ✓). No income reading from an iPhone on a school network or during school hours; the iPhone fact itself still shows
- **Targeting (Built, devlog 028):** "Advertisers pay more to show you an ad than they would for an Android user." ✓ Appodeal eCPM report (M-02; no multiplier until a figure is checked)
- **Targeting (Built, devlog 035, German wording by the user's decision):** "In a German lending study, iPhone users repaid more reliably than Android users: the gap was about as large as the one between an average credit score and a top-fifth one." ✓ Berg et al., *RFS* 2020 (A-02). No US equivalent found. Withheld by the teen guard

**T1-03 · Graphics hardware tier** · D · Obs (GPU) / Likely (tier) · **High-end tier Built (devlog 037)**
- Trigger: `X.webgl.unmaskedRenderer` or `X.webgpu`
- "Your computer's graphics hardware identifies itself as [NVIDIA GeForce RTX 4080], a high-end card, typically bought by people with money to spend on their computer."
- High-end list (`ENG_GPU_HIGH`): NVIDIA RTX xx70 and up (20–50 series), AMD RX 7800 and up and RX 9070, workstation cards (Quadro, RTX A-series/Ada/PRO, Radeon PRO W). A high-end card counts as a premium device for T2-A (teen-guarded)
- No price (devlog 037 decision: the tier matters, not the price). Source: the user's reasoning; no source ties card tier to income

**T1-04 · Apple chip tier** · D · Strong
- Trigger: renderer string names an Apple chip (e.g. `Apple M3 Max`)
- "Your Mac runs on an [M3 Max] chip, from the higher-priced tiers of Apple's lineup." (no price, devlog 037)
- Source: ✓ Apple pricing (needs a lookup table). Safari reports only "Apple GPU", so this works in Chromium/Firefox only

**T1-05 · Premium display** · D · Likely
- Trigger: `X.mediaPrefs.colorGamut` = p3/rec2020 **and** `dynamicRange` = high, on a non-Apple device
- "Your screen supports wide colour and HDR, features found mostly on higher-priced displays."
- Source: ?

**T1-06 · Out-of-support software** · D · Strong
- Trigger: `X.clientHints.platformVersion` (Windows 10 = major < 13), or browser version well behind current
- "Your computer runs Windows 10, which stopped receiving free security updates on October 14, 2025. About 1 in 7 US Windows computers still does." / "Your browser is [N] versions behind the current release."
- Source: ✓ Microsoft lifecycle page; ✓ StatCounter, Oct 2026: Windows 10 is 15% of US desktop Windows page views (B-10); ? current-version reference

**T1-07 · Low-cost device** · D · Likely ⚑
- Trigger: `S.isMobile` + `S.ram` ≤ 3 or `X.cpu.bestMs` in the slowest band + Android
- "Your phone has [2 GB] of memory and runs [N]× slower than a current flagship, which places it among lower-cost models [STAT: price band]."
- Source: ? needs a CPU benchmark baseline
- **Guard (Built by design, B-08):** never infer low income from Android alone; memory and speed are the low-cost signal (Android ownership is roughly flat across incomes in Pew 2013: ~, the flat-income detail wasn't on the page)

**T1-08 · Data saver** · D · Likely ⚑
- Trigger: `S.conn.save`
- "Your browser asks websites to use less data, a setting usually turned on to manage a limited data plan."
- Source: ~ rare in the US: about 2–4% of sessions in site samples, vs. 20% of requests from India and Brazil (Shopify, via Kadlec ✓; B-11). Supports reading it as a deliberate choice; weak extra support for T2-D

**T1-09 · Multiple monitors** · D · Obs
- Trigger: `X.screen.isExtended` (Chromium) or `availLeft` ≠ 0
- "You're using more than one monitor."
- Source: *n/a*

**T1-35 · Android phone** · D · Obs · **Built (devlog 033)**
- Trigger: Android user agent on a phone, only when T1-01 hasn't named the model (it would repeat it)
- "You're on an Android phone."
- **Targeting (Built, devlog 035, German wording):** "In a German lending study, Android users defaulted on payments about 1.7 times as often as iPhone users (1.79% vs. 1.07%)." ✓ Berg et al. 2020 (A-02). On T1-35, or on T1-01 when the model is known. Teen guard (school network or school hours) added in devlog 036
- **Guard:** no income reading from Android alone (B-08)

**T1-36 · Mac** · D · Obs · **Built (devlog 028)**
- Trigger: macOS user agent
- "You're on a Mac."
- **Targeting (Built, devlog 028; absorbed into T2-L when that fires):** "Orbitz has shown Mac users pricier hotels first, because its data showed Mac users spend as much as 30% more on hotels and are 40% more likely to book four- or five-star rooms." ~ company figures via ABC News, 2012 (A-03)
- **Not used as an income signal** (devlog 028: figure too old). In 2009, 36% of Mac users reported household income over $100,000 vs. 21% of PC users (~ GOOD, citing a 2009 study; dated) (B-07)

**T1-37 · Low battery** · D · Obs · **Built (devlog 033); Uber sentence added in devlog 034**
- Trigger: `S.battery.level` < 0.2 and not charging (Chromium only)
- "Your battery is at [12%] and not charging. Chrome-based browsers let any site read your battery level without asking."
- **Targeting (Built, devlog 034; ~ source, devlog 035):** "Uber's head of economic research said a low battery is one of the strongest predictors that someone will accept surge pricing. Uber says it doesn't use this to set fares." (A-40; ~ NPR *Hidden Brain* 2016 interview with Keith Chen, via TechRadar, Quartz and CBC; the interview itself not read first-hand)

### Location, language and origin

**T1-10 · Time-zone region** · Z · Obs
- Trigger: `S.tz`, only when T1-31 doesn't fire (lookup failed, or VPN/relay detected)
- "Your clock is set to [US Central] time."
- Source: *n/a* (replaces all city claims)

**T1-11 · Languages** · L · Obs
- Trigger: `S.langs` with more than one distinct base language
- "Your browser is set to [Spanish] first, then [English]."
- Source: *n/a*. Note: devices bought in the US default to English, so a non-English *first* language was usually chosen deliberately or the device was set up elsewhere
- **Targeting (Built):** "Ad systems read this setting to decide which language's ads to show you." (built as "…uses them to decide which language, and which ads, to show you") ~ Google Ads help: language settings are among the signals used (B-19)

**T1-12 · Language region vs. location** · L+I (or L+Z) · Likely ⚑
- Trigger: language region (e.g. `es-MX`) ≠ the connection country (IP lookup), or ≠ the time-zone country when the lookup failed or a VPN was detected
- "Your browser's language is set for [Mexico], and your connection is in the United States." (time-zone fallback: "…and your clock is set to [US Central] time.")
- The population statistic ("[number] people in the US speak Spanish at home") was moved to T2-C as an input: on its own it's about the country, not the visitor (devlog 018)

**T1-13 · Installed voices for other languages** · W · Likely ⚑
- Trigger: `X.voicesList` entries with `local: true` in languages other than the system language
- "Your device has text-to-speech voices installed for [Korean] and [Hindi], which usually come from language packs added by the owner."
- Source: ? Note: Chrome on desktop adds ~20 network "Google" voices in many languages; count only `local: true`

**T1-14 · Clock format** · **Removed (devlogs 026, 028).** Never fired (Implementation notes, devlog 010), and research found no use for it

**T1-30 · Keyboard layout** · W · Obs
- Trigger: `X.keyboardLayout` (Chromium) shows a non-US layout (AZERTY, QWERTZ, Spanish, Cyrillic, Arabic…)
- "Your keyboard is set to a [Spanish] layout, which usually reflects the language you type in every day."
- Supports T2-C (and T2-D). Source: *n/a*

**T1-31 · Location from IP** · I · Obs (devlog 017, 018)
- Trigger: `N.status==='ok'` with city, region or country, and no VPN/relay detected (T1-33)
- "Your internet connection places you in or near [High Falls, New York] (ZIP code [12440]), to within about [6] miles. Any site you visit can work this out from your IP address, without asking for your location."
- **Mobile carriers:** state or country only ("…places you in New York"). Carrier IPs locate the carrier's regional gateway, often far from the phone
- Replaces T1-10 when it fires. Accuracy shown only when the radius is ≤ 50 km. "US" checks across the engine (`isUS`) use the connection country when known. Source: MaxMind GeoLite2 via country.is
- **Accuracy (✓ B-02):** MaxMind places about 62% of US IPs within 50 km and 31% in a city more than 50 km away. Home connections are the most accurate (median 2.62 km in NYC, with MaxMind's *paid* data; this page uses the free version); mobile and **business** networks are worse. **Built (devlog 028):** unrecognized organization networks get state-level wording like mobile carriers; T1-32 says "registered to"
- **Targeting (Built, devlog 028, home networks only, R-07):** "Any site or link you open learns this much. On a home connection, paid location services can often place you within a few miles of where you live, which is why domestic-violence advocates recommend a VPN for people at risk." ✓ Saxon & Feamster 2021; NNEDV Safety Net
- **Targeting (Built (ZIP data), devlog 032, ACS income by ZIP, M-03; IRS skipped in devlog 030):** "In Google Ads, your area falls in the [top 10%] household-income tier. Advertisers can pay more to reach you there, or leave you out." ✓ Google Ads help (tiers, bid adjustments, exclusion); ~ the IRS basis is from secondary sources
- **Targeting (Built (ZIP data), devlog 032, when the ZIP's income is below the US median, A-04; A-05 is on T2-C):** "Online stores have charged different prices depending on where shoppers connect from. When the Wall Street Journal tested Staples' site, lower-income areas were less likely to get discounts." ~ WTOP summary of the WSJ (direction ✓; the WSJ's specific figures ✗ paywalled). Supporting case: Princeton Review prices by ZIP meant customers in areas with many Asian residents were 1.8× as likely to be offered higher prices (✓ ProPublica 2015)
- **Context (Built, devlog 028, M-01; on every T1-31 statement):** "When a site shows ads, details like your device, language, IP address and location are sent to the companies bidding to show you one." ✓ OpenRTB 2.6 device and geo objects

**T1-32 · Network type and provider** · I · Obs / Likely (devlog 017, 018)
- Trigger: `N.org`, no VPN/relay detected. Type from a hand-built name table (`ENG_PROVIDERS`, first match wins); quick pass in 018, to be extended and checked in research
- Home (Obs): "Your internet service is [Spectrum], a home internet provider, so your IP address points to a household connection. [Spectrum] can match that address to the account holder."
  - **Targeting (Built (ZIP data), devlog 032, B-01):** for AT&T, Verizon, EarthLink or CenturyLink in a below-median-income ZIP: "[AT&T] has been found to charge the same price for much slower internet in lower-income neighborhoods; in 92% of the cities where its speeds varied, poorer areas got the worse deals." ✓ The Markup, 2022
  - **Guard (B-04, closed in devlog 035):** a fiber provider isn't an income signal. Holds by omission (no rule reads fiber), so no source is needed
- Mobile (Likely; T-Mobile and Verizon also sell home internet over the same network): "You're connected through [Verizon]'s cellular network." Prepaid brands (Metro, Cricket, Boost) appear under the parent network, so prepaid can't be detected (B-09)
- Satellite (Obs): "…through [Starlink], a satellite internet service." **Built (devlog 028, B-03, Starlink only):** "…Over 85% of Starlink's US customers live in mostly rural areas, so you most likely live somewhere without cable or fiber." ✓ Recon Analytics 2024 and New Street Research 2026, via trade press
- University (Likely): "…through [Columbia University]'s network, so you're most likely on campus."
- School district (Likely ⚑, minors): "…a school district, so you're most likely a student or a member of school staff."
- Hospital or health system (Likely ⚑): "…so you're most likely a patient, a visitor or a member of staff."
  - **Targeting (Built, devlog 028, A-35, R-10):** "A third of top US hospital websites have sent visitors' appointment details to Facebook." ✓ The Markup, 2022 (33 of 100). Never a health conclusion about the visitor
- Public library (Likely ⚑): "…a public library." Counts as a cost signal for T2-B. ✓ Pew 2016: people using library computers and Wi-Fi skew young, Black, female and lower income (B-06; dated)
- Hotel, airport and public Wi-Fi operators (Likely): "…so you're most likely away from home."
- Military (Likely): "…a US military network."
  - **Targeting (Built, devlog 028, A-11, R-03):** "Researchers bought data on US servicemembers, including home addresses, net worth and religion, for as little as 12 cents a record. Ad-auction data has been used to target US military personnel and sent to firms in Russia and China." ✓ Duke 2023 via MIT Technology Review; ✓ ICCL 2023
- Prison and jail tablet providers (Likely ⚑): "…a company that runs tablets and internet access in prisons and jails."
- Government (Likely): "…a government network."
  - **Targeting (Built, devlog 035, reworded to the source, A-10):** "Lobbyists have aimed ads at the IP addresses of the White House." ✓ Washingtonian 2019 (a tourism lobby "sent their ads to IP addresses that covered the White House and the Trump hotel"). The broader vendor claim (FiscalNote, Point Blank Political) stays out: pages blocked
  - **Targeting (Built, devlog 028, R-03):** "Ad-auction data has been used to target US political decision makers, and sent to firms in Russia and China." ✓ ICCL 2023
- Other internet providers (Obs; names with "communications", "broadband", "wireless" etc.): "Your internet service is [name]." Kept separate so a small ISP isn't mistaken for an employer
- Anything else (Obs): "Your IP address is registered to [org]." This is where an employer's name appears → T2-G, T2-O

**T1-34 · Clock vs. connection time zone** · I · Likely (devlog 017, 018)
- Trigger: device time zone and IP time zone have different UTC offsets right now (not UTC, no VPN detected)
- "Your clock is set to [Pacific Time], but your connection is in [Eastern Time]. Most devices set their clock automatically, so this usually means you're connected through a VPN or your clock was set by hand. Less often, you've just traveled."
- Travel comes last because phones and most laptops update their time zone automatically when they move
- **Targeting (Built, devlog 028, B-18):** "Fraud-detection systems flag exactly this mismatch as a sign someone is hiding their location, so a purchase made now is more likely to face extra checks." ✓ SEON; ✓ Fingerprint (`timezone_mismatch`); ✗ Microsoft's equivalent (link failed)

### Work and daily life

**T1-15 · Desktop during working hours** · T · Obs (time) / Weak (at work)
- Trigger: `B.visitTime` weekday 9–17 local + `!S.isMobile`
- "You're on a computer at [2:10 PM] on a [Tuesday]."
- Source: *n/a*. The "at work" reading is only used in Tier 2

**T1-16 · Late night** · T · Obs
- Trigger: `B.visitTime.hour` 0–4
- "It's [2:14 AM] where you are."
- Source: *n/a*
- **Targeting (Built, devlog 035, German wording):** "In a German lending study, orders placed between midnight and 6 AM defaulted about twice as often as average." ✓ Berg et al. 2020 (A-02)
- **Targeting (optional, Built, devlog 028):** "Retailers' own surveys say late-night shoppers buy more on impulse: 39% say they do." ✓ CouponFollow 2026 (A-41; vendor survey)
- Not used: links between late-night use and mental health (A-33) or personality (B-15) are too weak for an individual

**T1-17 · Adobe software** · W · Likely
- Trigger: `X.fontsExtended.found` includes Adobe-only fonts (Acumin Pro, Adobe Caslon Pro, Trajan Pro, Bickham Script Pro, Kozuka Gothic Pro)
- "Your computer has fonts that come with Adobe's Creative Cloud apps, a paid subscription." (no price, devlog 037)
- Source: ✓ Adobe pricing page; ? which fonts are installed with Adobe apps vs. Acrobat Reader

**T1-18 · Programming fonts** · W · Likely
- Trigger: found fonts include JetBrains Mono, Fira Code, Hack, Inconsolata or IBM Plex Mono
- "Your computer has programming fonts that someone installed by hand."
- Source: ? Exclude Cascadia Code (ships with Windows Terminal) until checked

**T1-19 · Automated browser** · T · Strong
- Trigger: `X.webdriver === true`
- "This page is being read by automated software, not directly by a person."
- Source: ✓ WebDriver spec

### Privacy tools (conspicuous absence)

**T1-20 · Ad blocker** · P · Obs
- Trigger: `S.adblock` (ad-blocker bait). The Google Analytics variant was removed with GA (devlog 011)
- "An ad blocker is active in your browser. It did not stop the canvas fingerprint, because fingerprinting runs inside the page instead of loading anything from a tracker's servers."
- Source: *n/a*
- **Targeting (optional, Built, devlog 028):** "About a third of Americans use one (GWI, 2025). Advertisers' research describes ad-blocker users worldwide as mostly young, educated men." ~ GWI via Backlinko and Press Gazette (A-39; the demographic description is global)

**T1-21 · Fingerprint randomized** · P · Strong
- Trigger: `S.canvasStable === false` or `X.audioCheck.stable === false`
- "Asked to draw the same image twice, your browser gave two different answers. It is disguising your device."
- Source: ✓ Brave and Firefox fingerprinting-protection docs

**T1-22 · Tor or strict-protection pattern** · P · Strong
- Trigger: `S.tz` = UTC + `en-US` + window size in 200×100 steps (letterboxing) and/or randomized canvas
- "Your browser reports UTC time and a rounded window size, the pattern produced by Tor Browser's protections."
- Source: ✓ Tor Browser design document (letterboxing, UTC)
- **Targeting (optional, Built, devlog 028, B-24):** "Leaked NSA rules flagged people who searched for or visited the sites of privacy tools like Tails." ~ Schneier 2014 (source of the leak unclear)

**T1-23 · Do Not Track / Global Privacy Control** · P · Obs
- Trigger: `S.dnt === '1'`, `X.gpc === true`
- Built: "You've asked websites not to track you (Do Not Track). Most sites ignore this signal, and because few browsers send it, it makes yours slightly easier to single out." ✓ EFF Cover Your Tracks (B-22) / "Your browser sends Global Privacy Control, an opt-out signal recognized under California law and in several other states."
- **Built (ZIP data), devlog 032, B-23:** when the connection is in a state that requires it (`data/gpc-states.json`, 11 states after Maryland was removed in devlog 037): "In [California], sites are legally required to treat this as an opt-out of selling your data. Sephora paid $1.2 million for ignoring it." ✓ California AG 2022. The Sephora sentence applies to California only
- Requests, not blocking: on their own they don't trigger T2-F. Source: *n/a*

**T1-33 · VPN, data center or privacy relay** · P · Likely (devlog 017, 018)
- Trigger: `N.org` matches a VPN company or hosting/cloud provider in `ENG_PROVIDERS`
- VPN / data center: "Your connection comes from [M247], a data-center or VPN company, not a home or mobile provider. You're most likely using a VPN, which hides your real location from this page."
- Relay (Akamai, Cloudflare, Fastly): "…You're most likely using a privacy relay such as Apple's iCloud Private Relay, which hides your exact location but keeps your rough region."
- Counts as a blocking signal for T2-F; location rules (T1-31, T1-34) are skipped and the time zone is used instead
- Free substitute for paid VPN detection (`PRODUCT.md` → IP address). Misfires possible for people at cloud companies (e.g. Microsoft, Google offices)
- Context: 42% of US adults use a VPN for personal or work purposes (✓ Security.org 2026; B-05). A VPN alone isn't unusual, which is why T2-F needs a second privacy signal

### Accessibility ⚑

**T1-24 · Accessibility settings** · A · Obs
- Trigger: `X.mediaPrefs.forcedColors`, `contrast` = more, `invertedColors`, `S.reducedMotion`
- "Your device is set to [increase contrast] and [reduce motion]."
- Source: *n/a*. Base rates for T2-E (✓ A-30): Windows High Contrast Mode is on about 4% of active Windows machines (Microsoft figure, W3C 2019); about half of low-vision survey respondents use some high-contrast mode (WebAIM 2018). No base rate for reduced motion or dark mode (B-25)

**T1-25 · Reading-support fonts** · A · Likely
- Trigger: found fonts include OpenDyslexic or Atkinson Hyperlegible
- "You've installed [OpenDyslexic], a typeface designed for readers with dyslexia."
- Source: ✓ the typefaces' own documentation; ? no data on who installs them (A-31)

### Visit history and attention

**T1-26 · Return visits** · **Removed (devlog 037):** the page's cookies are now session cookies, so there is no visit history
- Trigger: `vis` cookie > 1 (the `_ga` part was removed with GA, devlog 011)
- "This is your [3rd] visit. This page recognizes you through a cookie it set the first time you came."
- Source: *n/a*

**T1-27 · Attention this visit** · T · Obs
- Trigger: `B.copies`, `B.tab`, `B.scroll.reversals`, `B.sectionsMs` (live)
- "You copied: '[…]'." / "You've left this tab [3] times, for [4 minutes] in total." / "You've spent longest on [Part Two]."
- Source: *n/a*
- **Targeting (Built, devlog 028, A-06):** "Pricing firms studied by the FTC say they use data down to mouse movements on a page to tailor prices." ✓ FTC surveillance-pricing study, initial findings, Jan 2025
- **Targeting (Built, devlog 028, B-14):** "Hundreds of popular sites record this same mouse, scroll and typing data with session-replay tools; some recordings captured passwords." ✓ Princeton CITP 2017 (482 of the top 50,000 sites, a lower bound)
- **Built (devlog 035, A-32; once 50+ mouse movements are recorded; third targeting sentence allowed):** "Researchers have screened for Parkinson's disease from mouse movements like the ones this page recorded." ✓ White, Doraiswamy & Horvitz 2018; White & Horvitz 2019. A capability statement, never a health claim about the visitor

**T1-28 · Browsing before arriving** · H · Obs
- Trigger: `S.histLen` > 1
- "This tab had [12] pages in its history before you arrived."
- Source: *n/a* (`history.length` caps at 50)

**T1-29 · Referrer** · H · Obs
- Trigger: `S.ref`
- "You arrived from [instagram.com]." / Google: "You arrived from a Google search. Your search terms weren't passed to this page."
- Source: *n/a*
- **Built (devlog 028, B-26):** for Reddit, TikTok or Instagram: "[Reddit] skews young: 40% of US adults under 30 use it, vs. 6% of those over 65." ✓ Pew 2026 (Instagram 79% / 21%, TikTok 65% / 11%). Adults only; no under-18 statements
- **Dropped (devlog 037; visitors won't arrive from ads):** arriving from an ad: "In lending research, customers who arrived by clicking an ad defaulted most often." ✓ Berg et al. 2020 (A-02), German data

---

## Part 3: Tier 2 threshold conclusions

The threshold is counted in **independent evidence groups**, not individual rules. Two rules from group D (say, iPhone + P3 display) are one purchase, so they count as one. Weak rules never count toward a threshold. These thresholds are judgment calls, explained per conclusion; they are not calculated probabilities (the page has no visitor data to calculate from).

**Status key:** **Built** = in `index.html` · **Planned** = designed, not yet built · **Held** = on hold by the user's decision. Items marked Built (ZIP data) use the bundled datasets in `data/` (devlog 032)

**T2-A · Income** · Likely ⚑ · Built (device + software path) / Built (ZIP data), devlog 032: location path
- Requires: a premium device (T1-02, T1-03, T1-04 or T1-05) **and** at least one more independent group: paid professional software (T1-17, group W), multiple monitors (T1-09 counts only if the device evidence is a phone), or **(Planned)** a high-income ZIP code (group I)
- US only (connection country, or time zone as fallback; sources are US)
- Built: "Your equipment and software are consistent with a higher-income household."
- **Not built as written (devlog 031 decision):** an income band for the person, not the area: "Based on your high-end hardware and the median income where you live, you most likely earn between [X] and [Y] a year. You most likely pay about [X] a month in rent, and are [Y]% likely to own your home." Needs ACS income, rent and home-ownership figures by ZIP, and a defensible way to combine them with device signals
- **Built (ZIP data), devlog 032, conflict:** premium device in a low-income ZIP → don't conclude (T2-J's area path isn't built, so it feeds nothing yet)
- Blocked by: T2-B (if both fire, show neither; show the Tier 1 facts)
- Source: ✓ Bertrand & Kamenica (A-01) for the device part; Census ACS for the area figures; ? for the combination (still no method source)
- **Research (devlog 026, C-01):**
  - ~~Device group adds Mac (T1-36, B-07 ~ dated)~~ Not built: the 2009 figure is too old (devlog 028)
  - Guards: no reading from an iPhone on a school network or in school hours (A-15, Built); fiber isn't an income signal (B-04, by omission); Android alone isn't a low-income signal (B-08, by omission)
  - **Honesty sentence (Built, devlog 028):** "Data brokers make this same guess about you, and they're often wrong: in one test, their income estimates put only 30–46% of people in the right bracket." ✓ Pew Research Center 2018 (voter-file vendors)
  - **Targeting (Built (ZIP data), devlog 032):** the Google Ads income-tier sentence (M-03), on T1-31, which T2-A absorbs

**T2-B · Tight budget** · Likely ⚑ · Built / Built (ZIP data), devlog 032: low-income ZIP path / Not buildable: mobile-only path
- Requires: low-cost device (T1-07, group D) **and** data saver (T1-08), out-of-support software (T1-06) or **a public library network** (T1-32, group I, devlog 018)
- **Low-income ZIP path (Built, devlog 032):** low-cost phone + a ZIP in the bottom 20% of US households by area income. **Mobile-only path (not buildable):** it needs the ZIP, but carrier connections only give a state (T1-31), so the two can't combine. Source kept (A-07, below)
- Note: T1-06/07/08 are all in group D. For this conclusion, data saver is a *setting* the owner chose, so it counts as separate from hardware. Accepted by the user (devlog 037)
- "Your phone and settings suggest you're managing costs carefully: a [2 GB] phone with data saver turned on."
- Source: ~ (parts sourced below; no source for the combination)
- **Research (devlog 026, C-02):**
  - Mobile path source (A-07 ✓): 29% of adults earning under $30K rely on a smartphone with no home broadband, vs. 7% of those earning $100K+ (Pew 2026). Caveat: being on cellular at home hours doesn't prove there's no home broadband, so this path also needs the ZIP or a library network
  - Data saver is rare in the US (B-11), supporting it as a deliberate choice; library users skew lower-income (B-06 ✓)
  - **Targeting (Built, devlog 028, A-08):** "Data brokers have sold lists of households like this under names like 'Hard Times', 'Rural and Barely Making It' and 'Credit Crunched: City Families'." ✓ US Senate Commerce Committee 2013 (both reports read)
  - **Targeting (Built, devlog 028, M-07; the payday-threat figure isn't in the built text):** "Cash-advance apps have run ads aimed at people living 'paycheck to paycheck'. 30% of online payday borrowers report being threatened by a lender or debt collector." ✓ Media Matters 2022; ✓ Pew Charitable Trusts 2014
  - **Targeting (Built, devlog 035, R-06; third targeting sentence allowed):** "Data brokers have sold lists of vulnerable people directly to scammers: one, Epsilon, agreed to pay $150 million in 2021 to resolve Justice Department charges." ~ DOJ press release (found by search; page renders empty to automated reads) and AARP 2021
  - Provider deal (B-01) and location pricing (A-04): see T1-32 and T1-31

**T2-C · Language community / likely ethnicity** · Likely ⚑ · Built / Built (ZIP data), devlog 032: ZIP language share
- Requires: in the US (connection, or time zone as fallback) + non-English first language (T1-11, group L) **and** independent support from group W (local voices T1-13, keyboard layout T1-30)
- **ZIP support (Built, devlog 032):** the ZIP's share of speakers of the visitor's language (ACS language group) counts as group I support when it is at least 5% and at least twice the US share; the statement gives both figures. The US-wide 93% figure is built here (below)
- "You most likely speak [Spanish] at home. Among people in the US who do, [STAT: share] identify as Hispanic or Latino."
- Applies only to languages where a Census figure exists
- Source: ✓ US Census Bureau, ACS
- **Research (devlog 026, C-08):**
  - `[STAT]` (✓ A-22, Built): about 93% of people who speak Spanish at home are Hispanic or Latino (34.8M vs. 2.8M; Pew analysis of 2011 ACS; needs a current figure)
  - **Targeting (Built, devlog 028, A-24):** "Facebook has labeled people's ethnicity from what they liked online and let advertisers leave them out of ads in its housing categories." ✓ ProPublica 2016
  - **Built (devlog 035, M-05; Spanish only; third targeting sentence allowed; also a T2-M label):** "Marketers sell Hispanic audience segments by language, by generation in the US (first, second, third) and by country of origin. A browser set to Spanish first is the kind of signal those labels are built on." ✓ Lotame segment sheet; ~ Claritas and Acxiom (pages blocked)
  - **Targeting (Built, devlog 028, A-23, Spanish only):** "Impersonation scams are the problem reported most often from majority-Latino communities." ~ FTC consumer posts (the originally cited page didn't contain it)
  - **Targeting (Built (ZIP data), devlog 032, A-05; fires when Asian-language speakers are ≥10% of the ZIP):** for Chinese, Korean, Vietnamese etc. with a ZIP: the Princeton Review case (see T1-31)

**T2-D · Device set up outside the US** · Likely ⚑ · Built
- Requires: language region ≠ connection country (T1-12, groups L+I; time zone as fallback) **and** a device-side signal from group D: an Android model code for a different region (e.g. Samsung's `F`/`B` international suffix vs. `U` for US carriers, from T1-01)
- "Your phone's model number marks it as an international version, not one sold by US carriers. Your device was most likely bought or set up outside the US."
- IP grounding (devlog 017): "in the US" now comes from the connection, independent of the device's own settings
- **Planned timeframe (needs a release-year table, not bundled; ⚑ wording on the review list):** the user's target, "…This suggests you've immigrated from [Mexico] to the US in the last [4] years." Needs the phone's release year (T1-01) and the 2–4 year replacement cycle (to verify). Research should also weigh the other readings the same signals fit: a visitor, a relative's phone, a device bought abroad
- Source: ? manufacturer model-code conventions
- **Research (devlog 026, C-07) · Decided in devlog 035:** Spanish dominance and the ICE sentence Built; the timeframe stays out
  - Spanish dominance (✓ B-20; Built for Spanish-first browsers, devlog 035): 61% of immigrant Hispanic adults are Spanish-dominant, vs. 8% of the US-born second generation (Pew 2012). Assumes a Spanish-first browser ≈ Spanish-dominant
  - Timeframe: phone release year + about 2.5-year replacement cycle (✓ A-26). **? no source** that immigrants bring their phones with them (searched). Keep the alternative readings in the text (visitor, relative's phone, device bought abroad); the timeframe stays Weak
  - **Targeting (Built, devlog 035, R-05, A-25):** "Immigration enforcement has bought commercial location and advertising data; in January 2026 ICE asked ad-tech companies for more. This is the kind of data that goes into it." ✓ The Register 2026 (the request); ✗ Axios on the 2025 purchase (blocked)
  - Scams (A-23) and marketers' segments (M-05): see T2-C

**T2-E · Low vision** · Likely ⚑ · Built, narrowed in devlog 037
- Requires: forced colors alone, **or** increased contrast plus one other visual setting (inverted colors, Atkinson Hyperlegible). Only the settings with a base rate count; reduced motion and dark mode don't
- "Your settings indicate you likely have low vision." The dyslexia conclusion was removed (no base rate); T1-25 still states the font
- Source: ~ (A-30 ✓): High Contrast Mode is on about 4% of Windows machines; about half of low-vision survey respondents use some high-contrast mode. No data for reduced motion, inverted colors or dyslexia fonts (A-31). **Stays at risk:** only forced colors / high contrast has any base rate

**T2-F · Actively hiding** · Strong · Built
- Requires: two or more privacy signals from group P (T1-20 to T1-23, T1-33), at least one of them blocking
- "You're actively hiding from trackers, and it's partly working. [What was blocked: ads, fingerprint, your real location.] [What wasn't: your approximate location and internet provider, time zone, screen, language, this visit's behavior.]"
- Source: *n/a* (observed). This is the conspicuous-absence exception
- **Research (devlog 026, C-10):** the DNT sentence (B-22) and Tails sentence (B-24) arrive through T1-23 and T1-22 (Built); the state GPC sentence (B-23) arrives the same way (Built, devlog 032); the two-signal rule is kept (VPNs are common, B-05)

**T2-G · At work at [employer]** · Likely · Built (devlog 018)
- Requires: an organization that isn't a known consumer provider or other network type (T1-32 "anything else", group I) **and** a computer on a weekday, 9 AM–5 PM local time (T1-15, group T)
- "Your IP address is registered to [Acme Corp]. You're on a computer at [2:10 PM] on a [Tuesday]. You're most likely at work, and [Acme Corp] is most likely your employer."
- Risk: an internet provider missing from the table reads as an employer. Mitigated by the "other internet providers" name patterns; the table needs a fuller pass in research
- Location caution (B-02): business networks geolocate poorly; say "registered to", not "located at"
- **Research (devlog 026, C-04):**
  - **Risk (Built, devlog 028, R-02):** "About three in four US employers log the websites staff visit. [Acme] can most likely see you came here, and when." ~ ExpressVPN survey of 1,500 employers, 2024, via Jibble (original gone; vendor-commissioned)
  - **Targeting (Built in part, devlog 028, A-09; the M-06 data co-op clause isn't in the built text):** "Sales software sold to websites names the company behind a visit so a sales team can follow up, and data co-ops track what each company's staff read and sell 'surges' in interest to vendors." ✓ Leadfeeder (10–40% of business traffic matched); ✓ Bombora
  - **Government variant (Built as part of T2-J, devlog 028):** with the government network type: "You're on a [state government] network during working hours. You most likely work in government." Plus the T1-32 government targeting sentences (A-10 ✗ source needed; R-03 ✓)

**T2-H · Living on campus** · Likely · Built (devlog 018)
- Requires: university network (T1-32, group I) **and** a visit between midnight and 5 AM (T1-16, group T)
- "…Being on a university network in the middle of the night suggests you live on campus, which usually means you're a student."
- Research (devlog 026): no change. A Mac adds nothing (40% of college students use one, Jamf 2019 ✓)

**T2-I · Away from home country** · Likely · Built (devlog 018)
- Requires: language region (L) and time zone (Z) agree on one country, **and** the connection (I) is in another, no VPN detected
- "Your language and clock are both set for [the United States], but you're connecting from [France]. You're most likely traveling, or using a VPN this page couldn't identify."
- **Targeting (Built, devlog 028, C-09):** "Hotel and travel sites have been found to change prices depending on where you connect from." ✓ Hupperich et al. 2017 (location-based price differences); ✓ Hannak et al. 2014. Plus the fraud-flag sentence from T1-34 (B-18)

**T2-J · Likely industry** · Likely ⚑ · Built: network path (devlog 028; school districts added in devlog 035) / Built: area path (devlog 037)
- **Area path (Built, devlog 037, the user's option B):** premium device or professional software (T1-17, T1-18) on a home connection in a low-income ZIP (bottom 20%), only when the network path hasn't fired. No claim about the visitor's job
- "Your equipment is well above what's typical for the area you're connecting from, where the median household income is [$24,086] a year. The highest wages in this ZIP code are in the [transportation, warehousing and utilities] industry. It's likely that advertisers will target you based on this information." (wording by the user)
- "Highest wages": highest median earnings (ACS B24031, `data/zip-industry-earnings.json`) among the 13 sectors with at least 50 local workers (from `zip-industry.json`) and a reliable estimate (MOE ≤30%); needs two such sectors
- Source: ACS for the figures. The advertiser sentence has no direct source; kept by the user's decision (exception to assembly rule 11)
- **Research (devlog 026, C-06):** a stronger path than the area one, which still has no person-level source:
  - Requires: network type (I) + weekday working hours (T) + computer (D). Hospital → healthcare; school district → education; university → higher education; government → public sector; military → armed forces
  - "On [Mercy Hospital]'s network on a weekday afternoon, on a computer: you most likely work in healthcare."
  - **School districts (Built, devlog 035):** fire only on a computer during weekday working hours ("you most likely work in education"); never a conclusion about a student (devlog 021)
  - The area path is built (above), without claiming the visitor's job

**T2-K · Night shift at the hospital** · **Dropped (devlog 037):** no source for a base rate
- Requires: hospital network (T1-32, I) + midnight–5 AM (T1-16, T) + computer (D). On a phone, no conclusion: patients and visitors fit equally
- "You're on [Mercy Hospital]'s network at [3:10 AM] on a computer. You're most likely working a night shift there."
- Plus the hospital targeting sentence (A-35, T1-32)
- Source: ✗ source needed. The night-work figure was withdrawn in the source check (devlog 025); the reasoning is sound but has no base rate. Searched again in devlog 035: no BLS figure for hospital night shifts found

**T2-L · How you'd be priced right now** · Strong (practices) · Built: device + clock groups (devlog 028) / Built (ZIP data), devlog 032: location group / Not built: behavior group (devlog 033)
- A sum of documented pricing practices, each triggered by an observed signal. Fires when signals from **two or more groups** apply: D (Mac → pricier hotels first, A-03; phone → different products, A-37; low battery → surge, A-40 ✗), I (location-based prices, A-04/A-05), Z (clock mismatch → fraud flag, B-18), T (mouse data in pricing, A-06)
- "Sites that adjust prices would read you like this: [a Mac user, the kind Orbitz showed pricier hotels first]; [your clock and connection disagree, which fraud systems flag]; [your area is one where online stores have offered fewer discounts]."
- Never claims this or any particular site is charging the visitor more
- **Behavior group (T, A-06) not built (devlog 033):** the profile is computed once, while behavior builds up over the visit, so the label would depend on when the profile rendered. The same FTC sentence already appears live in T1-27

**T2-M · Your ad profile** · Strong (practices) · Built: device, employer and privacy labels (devlog 028) / Built (ZIP data), devlog 032: income tier / Built, devlog 035: language segment (M-05, Spanish)
- Assembles the labels an ad system would attach, each from a fired signal with a source: price tier from the device (M-02, T1-02, T1-36), income tier from the ZIP (M-03), language segment (M-05, ⚑), employer and intent on an organization network (A-09, M-06), "hard to reach" if T2-F fires. Fires when **two or more groups** contribute
- "Here's roughly how an ad system would file you: [iPhone user: costs more to reach] · [top-10% income area] · [on Acme Corp's network]. Brokers' versions of these labels are often wrong (in one test, their income guesses were right 30–46% of the time), but they're still used."
- Never claims a specific company has filed *this* visitor. Income tier uses ACS income by ZIP (IRS skipped, devlog 030)

**T2-N · Servicemember under financial strain** · Likely ⚑ · Built (devlog 034)
- Requires: military network (T1-32, I) + tight-budget signals on a phone (T1-07/T1-08, D)
- "On a military network, with a phone and settings that suggest you're watching costs: this makes you a target for predatory organizations, like lenders and for-profit colleges." (wording by the user, devlog 034; replaces "this is the profile predatory lenders and for-profit colleges have targeted")
- Source: ~ CFPB (for-profit colleges ✓; predatory lenders near bases unverified); ~ about half of security-clearance denials in a 2007 sample involved finances (ClearanceJobs, secondary). Fires rarely

**T2-O · Narrowed to a few people** · Likely · Built (devlog 028)
- Requires: organization network (T1-32 "anything else", I) + city (T1-31, I) + an uncommon device model (D) or language (L)
- "Your employer's network, your city and a [Pixel 8 set to Korean] together narrow you to very few people. Matching this against a staff directory could be enough to name you." No crowd size is given; the page can't count it
- Source: ✓ Rocher, Hendrickx & de Montjoye 2019 (15 attributes re-identify 99.98% of Americans, modeled); ✓ Golle 2006 (ZIP + birth date + sex: 63%); ✓ Eckersley 2010 (83.6% of browsers unique). These use other attributes; the principle carries over, but no source measures this combination

**Reproductive health (R-04) · Decided in devlog 035: explanatory content in Part Three, later (not the engine):** documented risk, no inference. A week of visitor data for 600+ Planned Parenthood sites sold for about $160 (✓ Vice 2022); 155,000 people crossed state lines for abortion care in 2024 (✓ Guttmacher); Copley geofencing settlement (✗ Mass.gov blocked). The page can't identify clinic networks or a home state, so this could only appear as explanatory content

**Considered and dropped (devlog 018):** sunset and daylight times, distance to a landmark, and area-only statistics (ZIP demographics, county election results) as standalone statements. They describe the place, not the person, and feel like common knowledge. Area figures survive only as inputs (T2-A, T2-B, T2-C, T2-J). Rural vs. urban is kept as a possible input, not a statement

**Considered and dropped (devlog 026, from research):** politics from device (A-27, weak and contradictory); religion and sexuality (A-28, A-29, no signal; bad-actor context only); gender and age from mouse or typing (A-17, strong only in labs); a child or adult from touch (A-16, declined: minors, devlog 021); late-night use as a mental-health signal (A-33); night-owl personality (B-15); reading speed as education (B-16); cursor hesitation as uncertainty (B-17, non-monotonic); weekday daytime at home (A-13, too many readings); age from accessibility settings (A-19, no source); parenthood (A-20, no signal); prepaid plans (B-09, not detectable); dark mode (B-25, no base rate); search-engine choice (B-27); fiber as an income signal (B-04); Android as a low-income signal (B-08); clock format (T1-14, removed)

---

## Part 4: Assembly notes

1. **Selection:** compute all Tier 1 rules → compute Tier 2 → when a Tier 2 conclusion fires, its Tier 1 inputs move inside its paragraph as supporting facts instead of appearing separately.
2. **Duplicates:** at most one statement per evidence group per topic; keep the highest confidence.
3. **Contradictions:** define exclusive pairs (T2-A ↔ T2-B). If both fire, show neither conclusion; show the facts.
4. **Stacking:** at most [2] Weak statements per visitor (revisit after testing, devlog 037; no rule currently produces a Weak statement); Weak never feeds Tier 2. Prevents a pile of guesses reading as certainty.
5. **Ordering (proposal; deferred to the front-end pass, devlog 037):** what your device says → where and when you are → what it adds up to (Tier 2) → what you're doing right now (live behavior, T1-27).
6. **Timing:** passive rules are ready when `X.ready`; behavioral rules (T1-27) need time on the page and should update live or appear lower down.
7. **Browser coverage:** many signals are Chromium-only. Safari and Firefox visitors will get fewer statements. That's fine; say nothing rather than guess.
8. **Display:** confidence and sources are hidden in the main text; sources may go in a list at the bottom of the page (later).
9. **Area data is never shown alone** (devlog 018). It appears only inside a conclusion about the person.
10. **Targeting and risk statements follow their fact** (devlog 026). At most [2] per rule (revisit after testing, devlog 037; exceptions allowed by the user in devlog 035: T2-B with Epsilon, Spanish T2-C with M-05, T1-27 with Parkinson's; three each), in the paragraph after the observation they depend on. Optional ones are dropped first when a profile gets long.
11. **Unsourced items don't ship:** anything marked ✗ source needed, or US source needed, stays out of the build until a working source is found. **Exceptions by the user's decision:** T1-37's Uber sentence (A-40, devlog 034; now ~ sourced); the German lending study (A-02) ships with "German" in the wording (devlog 035); T2-J's area-path advertiser sentence and T1-03's high-end tier (devlog 037).

## Implementation notes (devlog 010)

Tier 1, Tier 2 and assembly are now built into `index.html` (between the `INFERENCE ENGINE START/END` markers) and tested with `node tests/run-personas.js` (results in `tests/results.md`). Differences from the draft above:

- **`[STAT]` clauses are omitted** until the research step. T2-C uses qualitative wording instead: "Most people in the US who speak Spanish at home identify as Hispanic or Latino."
- **Lookup-table rules show only the observed part:** T1-01 (model, no release year), T1-03 (GPU name, no price tier, so it doesn't feed T2-A), T1-04 (chip tier by name: Pro/Max/Ultra), T1-07 (memory ≤ 2 GB only, no CPU baseline), T1-06 (Windows version only, no browser-version check).
- **T1-09** uses `screen.isExtended` only. `availLeft` was dropped: a Dock on the left of a Mac screen produces false positives.
- **T1-13** runs on Windows only. macOS, iOS and Android ship voices for many languages by default.
- **T1-14 never fires.** Browsers format time using the locale's default, not the OS 24-hour setting, so `hourCycle` can't differ from what the language already says. Candidate for removal.
- **New: T1-30 keyboard layout** (group W), from the Part 5 gap; it supports T2-C. **GPC** was added to T1-23.
- **T2-D** recognizes international models for Samsung model codes only (suffix not starting with U/W).
- **T2-F** needs at least one *blocking* signal; Do Not Track/GPC alone are requests, not hiding.
- **T2-A ↔ T2-B conflict** can't occur on a real device under the current rules (T2-A needs desktop software, T2-B needs a phone); tested with a synthetic visitor (p20).
- **T1-27 (live)** is computed separately by `inferLive()` and refreshed every 3 seconds.
- **Devlog 011 (GA removed):** T1-20 now covers the ad-blocker bait only (the "your browser blocked this page's Google Analytics" variant is gone), and T1-26 now states only the visit count (the "Google first assigned your browser an ID" sentence is gone). Test visitors p05 and p06 still have GA-blocked data and targets; update them before the next run.
- **Devlog 017 (IP rules):** T1-31 to T1-34 added and T1-12/`isUS` updated (see Part 2). T2-F's "still visible" list adds "your approximate location and internet provider" when the IP lookup worked and no VPN was detected. The profile re-renders when the lookup finishes. Tier 2 thresholds otherwise unchanged. Test visitors have no `N` data, so their results are unaffected until IP data is added to them.
- **Devlog 018 (IP refinements):** mobile carriers get state-level location only; T1-34 reworded (VPN or manual clock before travel); relays (Akamai, Cloudflare, Fastly) get their own T1-33 wording and still show "your rough region" in T2-F; provider table extended (school district, hospital, library, public Wi-Fi, military, prisons, generic ISPs; "Universal" no longer matches universities); T2-B gains the library path; T2-G, T2-H, T2-I built. T2-A location path, T2-C ZIP support, T2-D timeframe and T2-J are Planned (need data).
- **Devlog 028 (build pass):** built everything source-checked that works with current data, except items on `PRODUCT.md` → "Review before building", anything needing income by ZIP or the state GPC list, and T2-N (held). Built: wording and targeting on T1-02 (incl. teen guard), T1-06, T1-16, T1-20, T1-22, T1-23, T1-27 (live), T1-29, T1-31, T1-32 (satellite, government, hospital, military), T1-34, T2-A, T2-B, T2-C, T2-G, T2-I; new T1-36, T2-J (network path), T2-L, T2-M, T2-O; T1-14 removed. Differences from the design above:
  - Teen guard: school hours = weekdays 7 AM–4 PM local; the advertiser sentence on T1-02 still shows (it isn't an income reading)
  - T1-31: state-level wording applies to unrecognized organization networks only (government, hospital, university keep city wording); the M-01 ad-auction sentence is on every T1-31 statement
  - T1-32: the rural sentence fires for Starlink only (the source is Starlink's customers)
  - T2-G's government variant is built as part of T2-J, so "you most likely work in government" isn't said twice
  - T2-J university wording is "work or study in higher education", since students use campus computers in working hours too
  - T2-L uses the device (Mac or phone) and clock-mismatch groups only; it absorbs T1-36 so the Orbitz sentence isn't repeated
  - T2-M labels: iPhone (US), employer network, privacy tools (when T2-F fires)
  - T2-O's "distinctive" = an Android model number or a non-English browser language
  - T2-F gets the DNT sentence through T1-23, which it absorbs
  - Not run: tests (p05/p06 and IP data still to update; no test visitors for the new rules yet). Syntax check only
- **Devlog 037 (decision backlog):**
  - T1-31: "in [state]" when no city is known; the M-03 tier sentence is removed when T2-M shows the tier (said once)
  - T1-03 high-end GPU tier (`ENG_GPU_HIGH`), feeding T2-A; price placeholders removed from T1-03, T1-04, T1-17
  - T2-E narrowed to low vision (forced colors, or increased contrast plus one other visual setting)
  - T2-J area path built with ACS earnings by industry (`data/zip-industry-earnings.json`, loaded with `zip-industry.json`)
  - Removed: T1-26 return visits (session cookies), device age, the ad-click lending sentence, T2-K
  - `gpc-states.json`: Maryland removed (its act lets businesses use an opt-out link *or* honor a signal, Md. Com. Law § 14-4607(F)(3), so honoring GPC isn't required); New Jersey's 2025-07-15 date confirmed
  - Checks: `node --check`; throwaway engine run in the scratchpad
- **Devlog 036:** teen guard extended to the Android lending sentence; fixed the doubled period after "Washington, D.C." in T1-31
- **Devlog 035 (group B, the user's review list):** built T2-J school districts; T2-D Spanish dominance and ICE sentences (timeframe out); M-05 in T2-C and T2-M; T1-27 Parkinson's (after 50+ mouse movements); T2-B Epsilon; A-02 in German wording on T1-02 (teen-guarded), T1-01/T1-35 and T1-16; A-10 reworded to the White House source. Not built: the ad-click lending sentence (no ad-click detection), T2-K (still no source), R-04 (Part Three later). Checks: `node --check`; throwaway engine run in the scratchpad
- **Devlog 034:** T2-N built with the user's wording (fires on a phone only, matching "a phone and settings"); T1-37's Uber sentence added without a checked source (exception to assembly rule 11). Checks: `node --check`; throwaway engine run in the scratchpad
- **Devlog 033 (group C):** built T1-35 (Android, only without a T1-01 model) and T1-37 (low battery, observed fact only). Not built: T1-37's Uber sentence and T2-K (✗ source needed, on the review list), T2-L's behavior group (timing, see T2-L), T2-N (held; put back to the user). Checks: `node --check`; throwaway engine run in the scratchpad
- **Devlog 032 (area data):** the engine reads `D.Z` (Census tables and the GPC list from `data/`, loaded by the page for US connections only). Without it, nothing below fires and all other rules are unchanged. Differences from the design:
  - **When the ZIP is used:** only on home, small-ISP or satellite connections in the US, located to within 50 km. Organization, campus, hospital, library and carrier networks don't describe where someone lives
  - **Reliable income only:** at least 200 households and a margin of error ≤30% of the estimate, or top-coded at $250,000+ (no margin published). Covers 97% of US households
  - **Income tiers:** ZIPs ranked by median household income, weighted by households (the share of US households in lower-income ZIPs). High = top 20%; low = bottom 20%; "below the US median" = under $80,734 (ACS 2020–2024). Google Ads' own tiers are said to use IRS data, so the sentence says "by Census figures"
  - **T2-A income band (decision, devlog 031):** no range is claimed for the person. The conclusion states the ZIP's median income, rent and home-ownership share as figures for everyone in the area. Fires on premium device + high-income ZIP, or premium device + paid software; a premium device in a low-income ZIP suppresses it
  - **T1-31 targeting:** at most two with R-07. Home: R-07 + (A-04 if below the US median, else M-03). Small ISP or satellite: M-03, plus A-04 if below the median. When T2-L uses the location label, A-04 is removed from T1-31 so it's said once
  - **T2-M:** the income-tier label appears on home connections, so the tier can be said twice (T1-31 and T2-M)
  - **B-01:** AT&T, Verizon and EarthLink by brand, CenturyLink/Qwest by organization name; EarthLink added to the provider table
  - **T1-23:** the state sentence replaces the generic GPC sentence when the requirement is in force (date checked on the visitor's clock); Sephora sentence California only
  - Not built: T2-B's mobile-only path (see T2-B)
  - Checks: inline scripts pass `node --check`; a throwaway run of the engine on five synthetic visitors (scratchpad, not `tests/`) confirmed each new path and the no-data case
- **Devlog 031 (statuses tidied):** every rule's status now matches `index.html`. Items built in devlog 028 are marked Built; items waiting on `data/` are marked Planned (ZIP data); T2-N is Held. No code changed.
- **Devlog 026 (research folded in):** design only. Nothing new is built; T1-14 is marked for removal at the next build. Planned items: T1-35 to T1-37, T2-K to T2-O, plus guards and targeting sentences across existing rules.
- **Devlog 013:** statements for unavailable data removed (no-referrer statement; "your search terms weren't passed"). Short "what this means" clauses added to T1-01, 03, 06, 09, 10, 11, 15/16, 20, 23, 24, 26, 27, 28, 29, 30. T1-21 rewritten in the user's wording, with the silent sound folded in.

## Part 5: Gaps noticed (not collected, would strengthen rules)

- **City-level location:** collected in recorder `N` (devlog 015) and used by the engine since devlog 017 (T1-31 to T1-34).
- **Research status (devlog 026):** sourced: the replacement cycle (A-26), smartphone-only internet by income (A-07), IP location accuracy (B-02). Still open: a provider-table pass, ACS/IRS bundles, release-year table.
- **Data the research step needs** (devlog 018), all public and bundleable in the page so no visitor data leaves it:
  - ✓ **Bundled (devlog 030):** Census ACS by ZIP (ZCTA): median household income, median rent, home-ownership rate, language spoken at home, industry of employment → T2-A, T2-B, T2-C, T2-J. Files in `data/` (see `data/README.md`); not yet read by the engine
  - Industry wages by area (County Business Patterns, BLS) → T2-J
  - Phone model → release year table → T1-01 device age, T2-D timeframe
  - A source for the 2–4 year phone replacement cycle → T2-D
  - Smartphone-only internet use by income (e.g. Pew) → T2-B mobile path
  - GeoLite2 accuracy in the US → T1-31's "within X miles"
  - A fuller, checked provider table (US ISPs, campus, hospital, public Wi-Fi, VPN and hosting networks) → T1-32, T1-33, T2-G
  - Rural–urban codes by ZIP (USDA RUCA) → possible input
- **Added by research (devlog 026):**
  - ~~IRS income by ZIP (Statistics of Income, public) → M-03 income tier, T2-M~~ **Skipped (devlog 030):** M-03 and T2-M will use ACS income instead, a looser match to Google Ads' tiers
  - ✓ **Bundled (devlog 030):** State list of laws requiring Global Privacy Control → T1-23 (B-23). `data/gpc-states.json`, 12 states; Maryland and New Jersey dates to confirm against statute
  - Provider table additions: satellite providers (B-03), state and federal government organizations (C-04, T2-J), the four providers in The Markup's study (B-01); confirm prepaid brands have no separate ASNs (B-09)
  - A Mac / Android / battery reading in the engine (T1-35 to T1-37)
  - Working sources for: A-10 (lobbying), A-40 (Uber), R-06 (Epsilon), A-04's figures, B-04 (fiber), US equivalents for A-02
- **Keyboard layout** (`navigator.keyboard.getLayoutMap()`, Chromium): independent support for T2-C/T2-D.
- **Global Privacy Control** (`navigator.globalPrivacyControl`): a modern counterpart to Do Not Track for group P.
