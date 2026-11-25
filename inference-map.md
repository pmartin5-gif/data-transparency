# Inference Map

**Status:** designed without new research (devlog 008), built into `index.html` (devlogs 010, 017, 018) and shown in the profile. Sources and `[STAT: …]` placeholders wait for the research step. Rules marked **Planned** are designed but need data the page doesn't have yet.

**Standards**
- Reflect back what we *know* about the visitor. If something can't be inferred, say nothing. The exception is **conspicuous absence**: privacy tools that hide or fake data.
- Accuracy over alarm. Where it's a likelihood, the statement says so.
- Confidence and sources are tracked here for review. Display is decided later (sources likely at the bottom of the page).
- Gender and sexuality are absent on purpose: no collected signal supports a truthful inference.
- **Aim for "how did it know that?"** (devlog 018). A statement should reveal something hidden about *this person*. Common knowledge (e.g. sunset times) and facts about an area (e.g. a ZIP code's median income) don't produce that moment, so they are never statements on their own.
- **Area data is an input, not a statement.** Census or industry figures for a ZIP code or county only feed conclusions about the person: income band, rent, home ownership, likely industry. Target boldness, from the user: "Based on your high-end hardware and the median income of your location, it's likely you make between X and Y per year. You pay X in rent per month and are Y% likely to own your home."
- **Targeting statements belong in the profile** when they are truthful, considered and impactful (devlog 019; replaces Part 1, problem 8 for these). How advertisers, marketers or bad actors would characterize or target the visitor counts, even when their inference is unreliable. See `research-plan.md` §4.
- **Network type and employer are high-value.** What kind of network someone is on (workplace, campus, hospital, library, prison) says a lot about who they are and where they are right now.

**Keys**
- **Confidence (provisional):** **Obs** = observed fact · **Strong** = well documented, few exceptions · **Likely** = real correlation, many exceptions · **Weak** = pattern used by industry, often wrong
- **Source:** ✓ = known source, named (still to be checked in research) · **?** = needs source · *n/a* = observed, no source needed
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
| Behavior | Battery <20% → "urgency, reduced purchase deliberation" | **Cut** | Rests on an anecdote (Uber, 2016) the company said it didn't act on. Battery level itself can be observed |
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
- **Planned (device age):** with release years, "you've most likely had this phone for about [N] years." People typically replace phones every 2–4 years (user's figure, to verify), so a release year bounds when the phone was bought. Feeds T2-D's timeframe ("in the last [N] years")

**T1-02 · iPhone** · D · Likely
- Trigger: `S.isIOS` (phone screen)
- "You're on an iPhone. In the US, owning an iPhone is one of the strongest single predictors of high household income [STAT: accuracy/share]."
- Source: ✓ Bertrand & Kamenica, "Coming Apart? Cultural Distances in the United States over Time," NBER WP 24771 (2018)

**T1-03 · Graphics hardware tier** · D · Obs (GPU) / Likely (tier)
- Trigger: `X.webgl.unmaskedRenderer` or `X.webgpu`
- "Your computer has a [NVIDIA GeForce RTX 4080], a high-end graphics card typically found in gaming and workstation machines costing [STAT: typical system price]."
- Source: ? needs a GPU → tier/price table

**T1-04 · Apple chip tier** · D · Strong
- Trigger: renderer string names an Apple chip (e.g. `Apple M3 Max`)
- "Your Mac runs on an [M3 Max] chip, sold only in configurations starting at [STAT: price]."
- Source: ✓ Apple pricing (needs a lookup table). Safari reports only "Apple GPU", so this works in Chromium/Firefox only

**T1-05 · Premium display** · D · Likely
- Trigger: `X.mediaPrefs.colorGamut` = p3/rec2020 **and** `dynamicRange` = high, on a non-Apple device
- "Your screen supports wide colour and HDR, features found mostly on higher-priced displays."
- Source: ?

**T1-06 · Out-of-support software** · D · Strong
- Trigger: `X.clientHints.platformVersion` (Windows 10 = major < 13), or browser version well behind current
- "Your computer runs Windows 10, which stopped receiving free security updates on October 14, 2025." / "Your browser is [N] versions behind the current release."
- Source: ✓ Microsoft lifecycle page; ? current-version reference

**T1-07 · Low-cost device** · D · Likely ⚑
- Trigger: `S.isMobile` + `S.ram` ≤ 3 or `X.cpu.bestMs` in the slowest band + Android
- "Your phone has [2 GB] of memory and runs [N]× slower than a current flagship, which places it among lower-cost models [STAT: price band]."
- Source: ? needs a CPU benchmark baseline

**T1-08 · Data saver** · D · Likely ⚑
- Trigger: `S.conn.save`
- "Your browser asks websites to use less data, a setting usually turned on to manage a limited data plan."
- Source: ?

**T1-09 · Multiple monitors** · D · Obs
- Trigger: `X.screen.isExtended` (Chromium) or `availLeft` ≠ 0
- "You're using more than one monitor."
- Source: *n/a*

### Location, language and origin

**T1-10 · Time-zone region** · Z · Obs
- Trigger: `S.tz`, only when T1-31 doesn't fire (lookup failed, or VPN/relay detected)
- "Your clock is set to [US Central] time."
- Source: *n/a* (replaces all city claims)

**T1-11 · Languages** · L · Obs
- Trigger: `S.langs` with more than one distinct base language
- "Your browser is set to [Spanish] first, then [English]."
- Source: *n/a*. Note: devices bought in the US default to English, so a non-English *first* language was usually chosen deliberately or the device was set up elsewhere

**T1-12 · Language region vs. location** · L+I (or L+Z) · Likely ⚑
- Trigger: language region (e.g. `es-MX`) ≠ the connection country (IP lookup), or ≠ the time-zone country when the lookup failed or a VPN was detected
- "Your browser's language is set for [Mexico], and your connection is in the United States." (time-zone fallback: "…and your clock is set to [US Central] time.")
- The population statistic ("[number] people in the US speak Spanish at home") was moved to T2-C as an input: on its own it's about the country, not the visitor (devlog 018)

**T1-13 · Installed voices for other languages** · W · Likely ⚑
- Trigger: `X.voicesList` entries with `local: true` in languages other than the system language
- "Your device has text-to-speech voices installed for [Korean] and [Hindi], which usually come from language packs added by the owner."
- Source: ? Note: Chrome on desktop adds ~20 network "Google" voices in many languages; count only `local: true`

**T1-14 · Clock format** · L · Weak
- Trigger: `X.locale.hourCycle` = h23 in a US time zone with an `en-US` locale
- "Your device shows time in 24-hour format, which is uncommon in the US."
- Source: ? Note: depends on locale; only meaningful when the locale is `en-US`
- **Never fires** (see Implementation notes, devlog 010). Candidate for removal

**T1-30 · Keyboard layout** · W · Obs
- Trigger: `X.keyboardLayout` (Chromium) shows a non-US layout (AZERTY, QWERTZ, Spanish, Cyrillic, Arabic…)
- "Your keyboard is set to a [Spanish] layout, which usually reflects the language you type in every day."
- Supports T2-C (and T2-D). Source: *n/a*

**T1-31 · Location from IP** · I · Obs (devlog 017, 018)
- Trigger: `N.status==='ok'` with city, region or country, and no VPN/relay detected (T1-33)
- "Your internet connection places you in or near [High Falls, New York] (ZIP code [12440]), to within about [6] miles. Any site you visit can work this out from your IP address, without asking for your location."
- **Mobile carriers:** state or country only ("…places you in New York"). Carrier IPs locate the carrier's regional gateway, often far from the phone
- Replaces T1-10 when it fires. Accuracy shown only when the radius is ≤ 50 km. "US" checks across the engine (`isUS`) use the connection country when known. Source: MaxMind GeoLite2 via country.is; ? accuracy in the US (research)

**T1-32 · Network type and provider** · I · Obs / Likely (devlog 017, 018)
- Trigger: `N.org`, no VPN/relay detected. Type from a hand-built name table (`ENG_PROVIDERS`, first match wins); quick pass in 018, to be extended and checked in research
- Home (Obs): "Your internet service is [Spectrum], a home internet provider, so your IP address points to a household connection. [Spectrum] can match that address to the account holder."
- Mobile (Likely; T-Mobile and Verizon also sell home internet over the same network): "You're connected through [Verizon]'s cellular network."
- Satellite (Obs): "…through [Starlink], a satellite internet service."
- University (Likely): "…through [Columbia University]'s network, so you're most likely on campus."
- School district (Likely ⚑, minors): "…a school district, so you're most likely a student or a member of school staff."
- Hospital or health system (Likely ⚑): "…so you're most likely a patient, a visitor or a member of staff."
- Public library (Likely ⚑): "…a public library." Counts as a cost signal for T2-B
- Hotel, airport and public Wi-Fi operators (Likely): "…so you're most likely away from home."
- Military (Likely): "…a US military network."
- Prison and jail tablet providers (Likely ⚑): "…a company that runs tablets and internet access in prisons and jails."
- Government (Likely): "…a government network."
- Other internet providers (Obs; names with "communications", "broadband", "wireless" etc.): "Your internet service is [name]." Kept separate so a small ISP isn't mistaken for an employer
- Anything else (Obs): "Your IP address is registered to [org]." This is where an employer's name appears → T2-G

**T1-34 · Clock vs. connection time zone** · I · Likely (devlog 017, 018)
- Trigger: device time zone and IP time zone have different UTC offsets right now (not UTC, no VPN detected)
- "Your clock is set to [Pacific Time], but your connection is in [Eastern Time]. Most devices set their clock automatically, so this usually means you're connected through a VPN or your clock was set by hand. Less often, you've just traveled."
- Travel comes last because phones and most laptops update their time zone automatically when they move

### Work and daily life

**T1-15 · Desktop during working hours** · T · Obs (time) / Weak (at work)
- Trigger: `B.visitTime` weekday 9–17 local + `!S.isMobile`
- "You're on a computer at [2:10 PM] on a [Tuesday]."
- Source: *n/a*. The "at work" reading is only used in Tier 2

**T1-16 · Late night** · T · Obs
- Trigger: `B.visitTime.hour` 0–4
- "It's [2:14 AM] where you are."
- Source: *n/a*

**T1-17 · Adobe software** · W · Likely
- Trigger: `X.fontsExtended.found` includes Adobe-only fonts (Acumin Pro, Adobe Caslon Pro, Trajan Pro, Bickham Script Pro, Kozuka Gothic Pro)
- "Your computer has fonts that come with Adobe's Creative Cloud, a subscription costing [STAT: price] a month."
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

**T1-21 · Fingerprint randomized** · P · Strong
- Trigger: `S.canvasStable === false` or `X.audioCheck.stable === false`
- "Asked to draw the same image twice, your browser gave two different answers. It is disguising your device."
- Source: ✓ Brave and Firefox fingerprinting-protection docs

**T1-22 · Tor or strict-protection pattern** · P · Strong
- Trigger: `S.tz` = UTC + `en-US` + window size in 200×100 steps (letterboxing) and/or randomized canvas
- "Your browser reports UTC time and a rounded window size, the pattern produced by Tor Browser's protections."
- Source: ✓ Tor Browser design document (letterboxing, UTC)

**T1-23 · Do Not Track / Global Privacy Control** · P · Obs
- Trigger: `S.dnt === '1'`, `X.gpc === true`
- "You've asked websites not to track you (Do Not Track). Most sites ignore this signal…" / "Your browser sends Global Privacy Control, an opt-out signal recognized under California law and in several other states."
- Requests, not blocking: on their own they don't trigger T2-F. Source: *n/a*

**T1-33 · VPN, data center or privacy relay** · P · Likely (devlog 017, 018)
- Trigger: `N.org` matches a VPN company or hosting/cloud provider in `ENG_PROVIDERS`
- VPN / data center: "Your connection comes from [M247], a data-center or VPN company, not a home or mobile provider. You're most likely using a VPN, which hides your real location from this page."
- Relay (Akamai, Cloudflare, Fastly): "…You're most likely using a privacy relay such as Apple's iCloud Private Relay, which hides your exact location but keeps your rough region."
- Counts as a blocking signal for T2-F; location rules (T1-31, T1-34) are skipped and the time zone is used instead
- Free substitute for paid VPN detection (`PRODUCT.md` → IP address). Misfires possible for people at cloud companies (e.g. Microsoft, Google offices)

### Accessibility ⚑

**T1-24 · Accessibility settings** · A · Obs
- Trigger: `X.mediaPrefs.forcedColors`, `contrast` = more, `invertedColors`, `S.reducedMotion`
- "Your device is set to [increase contrast] and [reduce motion]."
- Source: *n/a*

**T1-25 · Reading-support fonts** · A · Likely
- Trigger: found fonts include OpenDyslexic or Atkinson Hyperlegible
- "You've installed [OpenDyslexic], a typeface designed for readers with dyslexia."
- Source: ✓ the typefaces' own documentation

### Visit history and attention

**T1-26 · Return visits** · H · Obs
- Trigger: `vis` cookie > 1 (the `_ga` part was removed with GA, devlog 011)
- "This is your [3rd] visit. This page recognizes you through a cookie it set the first time you came."
- Source: *n/a*

**T1-27 · Attention this visit** · T · Obs
- Trigger: `B.copies`, `B.tab`, `B.scroll.reversals`, `B.sectionsMs` (live)
- "You copied: '[…]'." / "You've left this tab [3] times, for [4 minutes] in total." / "You've spent longest on [Part Two]."
- Source: *n/a*

**T1-28 · Browsing before arriving** · H · Obs
- Trigger: `S.histLen` > 1
- "This tab had [12] pages in its history before you arrived."
- Source: *n/a* (`history.length` caps at 50)

**T1-29 · Referrer** · H · Obs
- Trigger: `S.ref`
- "You arrived from [instagram.com]." / Google: "You arrived from a Google search. Your search terms weren't passed to this page."
- Source: *n/a*

---

## Part 3: Tier 2 threshold conclusions

The threshold is counted in **independent evidence groups**, not individual rules. Two rules from group D (say, iPhone + P3 display) are one purchase, so they count as one. Weak rules never count toward a threshold. These thresholds are judgment calls, explained per conclusion; they are not calculated probabilities (the page has no visitor data to calculate from).

**Status key:** **Built** = in `index.html` · **Planned** = designed, needs data the page doesn't have yet (research step)

**T2-A · Income** · Likely ⚑ · Built (device + software path) / Planned (location path)
- Requires: a premium device (T1-02, T1-03, T1-04 or T1-05) **and** at least one more independent group: paid professional software (T1-17, group W), multiple monitors (T1-09 counts only if the device evidence is a phone), or **(Planned)** a high-income ZIP code (group I)
- US only (connection country, or time zone as fallback; sources are US)
- Built: "Your equipment and software are consistent with a higher-income household."
- **Planned target:** an income band for the person, not the area: "Based on your high-end hardware and the median income where you live, you most likely earn between [X] and [Y] a year. You most likely pay about [X] a month in rent, and are [Y]% likely to own your home." Needs ACS income, rent and home-ownership figures by ZIP, and a defensible way to combine them with device signals
- **Planned conflict:** premium device in a low-income ZIP → don't conclude; feeds T2-J instead
- Blocked by: T2-B (if both fire, show neither; show the Tier 1 facts)
- Source: ✓ Bertrand & Kamenica (2018) for the device part; ✓ Census ACS for the area figures; ? for the combination

**T2-B · Tight budget** · Likely ⚑ · Built
- Requires: low-cost device (T1-07, group D) **and** data saver (T1-08), out-of-support software (T1-06) or **a public library network** (T1-32, group I, devlog 018)
- **Planned:** mobile-only internet (cellular connection at home hours, group I) and a low-income ZIP as further paths. Needs a source on smartphone-only internet users by income (e.g. Pew)
- Note: T1-06/07/08 are all in group D. For this conclusion, data saver is a *setting* the owner chose, so it counts as separate from hardware. Flagged for review
- "Your phone and settings suggest you're managing costs carefully: a [2 GB] phone with data saver turned on. [STAT: share of prepaid/limited-plan users by income]."
- Source: ?

**T2-C · Language community / likely ethnicity** · Likely ⚑ · Built
- Requires: in the US (connection, or time zone as fallback) + non-English first language (T1-11, group L) **and** independent support from group W (local voices T1-13, keyboard layout T1-30)
- **Planned:** the ZIP code's share of [Spanish] speakers as further support (group I), and the US-wide figure moved here from T1-12
- "You most likely speak [Spanish] at home. Among people in the US who do, [STAT: share] identify as Hispanic or Latino."
- Applies only to languages where a Census figure exists
- Source: ✓ US Census Bureau, ACS

**T2-D · Device set up outside the US** · Likely ⚑ · Built
- Requires: language region ≠ connection country (T1-12, groups L+I; time zone as fallback) **and** a device-side signal from group D: an Android model code for a different region (e.g. Samsung's `F`/`B` international suffix vs. `U` for US carriers, from T1-01)
- "Your phone's model number marks it as an international version, not one sold by US carriers. Your device was most likely bought or set up outside the US."
- IP grounding (devlog 017): "in the US" now comes from the connection, independent of the device's own settings
- **Planned timeframe:** the user's target, "…This suggests you've immigrated from [Mexico] to the US in the last [4] years." Needs the phone's release year (T1-01) and the 2–4 year replacement cycle (to verify). Research should also weigh the other readings the same signals fit: a visitor, a relative's phone, a device bought abroad
- Source: ? manufacturer model-code conventions

**T2-E · Disability** · Likely / Weak ⚑
- Requires: two different accessibility mechanisms (two of: forced colors, increased contrast, inverted colors, reading-support font T1-25), **or** one strong one (forced colors, inverted colors, OpenDyslexic). Reduced motion and dark mode alone don't count
- "Your settings indicate you likely have [low vision / dyslexia]."
- Source: ? usage rates of these settings among disabled vs. non-disabled users. **At risk of being cut** in the research step if no data exists

**T2-F · Actively hiding** · Strong · Built
- Requires: two or more privacy signals from group P (T1-20 to T1-23, T1-33), at least one of them blocking
- "You're actively hiding from trackers, and it's partly working. [What was blocked: ads, fingerprint, your real location.] [What wasn't: your approximate location and internet provider, time zone, screen, language, this visit's behavior.]"
- Source: *n/a* (observed). This is the conspicuous-absence exception

**T2-G · At work at [employer]** · Likely · Built (devlog 018)
- Requires: an organization that isn't a known consumer provider or other network type (T1-32 "anything else", group I) **and** a computer on a weekday, 9 AM–5 PM local time (T1-15, group T)
- "Your IP address is registered to [Acme Corp]. You're on a computer at [2:10 PM] on a [Tuesday]. You're most likely at work, and [Acme Corp] is most likely your employer."
- Risk: an internet provider missing from the table reads as an employer. Mitigated by the "other internet providers" name patterns; the table needs a fuller pass in research

**T2-H · Living on campus** · Likely · Built (devlog 018)
- Requires: university network (T1-32, group I) **and** a visit between midnight and 5 AM (T1-16, group T)
- "…Being on a university network in the middle of the night suggests you live on campus, which usually means you're a student."

**T2-I · Away from home country** · Likely · Built (devlog 018)
- Requires: language region (L) and time zone (Z) agree on one country, **and** the connection (I) is in another, no VPN detected
- "Your language and clock are both set for [the United States], but you're connecting from [France]. You're most likely traveling, or using a VPN this page couldn't identify."

**T2-J · Likely industry** · Likely ⚑ · Planned (devlog 018)
- Requires: premium device or professional software (D/W) **and** an area whose median income is low but where one industry pays well above it (I + area data)
- Target: "Your hardware is well above what's typical where you live, and the best-paid work here is in [healthcare]. You most likely work in [healthcare]." Can combine with professional fonts (T1-17 creative, T1-18 programming)
- Needs Census industry-by-area data (ACS industry by ZIP/county; County Business Patterns) and wage figures by industry

**Considered and dropped (devlog 018):** sunset and daylight times, distance to a landmark, and area-only statistics (ZIP demographics, county election results) as standalone statements. They describe the place, not the person, and feel like common knowledge. Area figures survive only as inputs (T2-A, T2-B, T2-C, T2-J). Rural vs. urban is kept as a possible input, not a statement

---

## Part 4: Assembly notes

1. **Selection:** compute all Tier 1 rules → compute Tier 2 → when a Tier 2 conclusion fires, its Tier 1 inputs move inside its paragraph as supporting facts instead of appearing separately.
2. **Duplicates:** at most one statement per evidence group per topic; keep the highest confidence.
3. **Contradictions:** define exclusive pairs (T2-A ↔ T2-B). If both fire, show neither conclusion; show the facts.
4. **Stacking:** at most [2] Weak statements per visitor; Weak never feeds Tier 2. Prevents a pile of guesses reading as certainty.
5. **Ordering (proposal):** what your device says → where and when you are → what it adds up to (Tier 2) → what you're doing right now (live behavior, T1-27).
6. **Timing:** passive rules are ready when `X.ready`; behavioral rules (T1-27) need time on the page and should update live or appear lower down.
7. **Browser coverage:** many signals are Chromium-only. Safari and Firefox visitors will get fewer statements. That's fine; say nothing rather than guess.
8. **Display:** confidence and sources are hidden in the main text; sources may go in a list at the bottom of the page (later).
9. **Area data is never shown alone** (devlog 018). It appears only inside a conclusion about the person.

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
- **Devlog 013:** statements for unavailable data removed (no-referrer statement; "your search terms weren't passed"). Short "what this means" clauses added to T1-01, 03, 06, 09, 10, 11, 15/16, 20, 23, 24, 26, 27, 28, 29, 30. T1-21 rewritten in the user's wording, with the silent sound folded in.

## Part 5: Gaps noticed (not collected, would strengthen rules)

- **City-level location:** collected in recorder `N` (devlog 015) and used by the engine since devlog 017 (T1-31 to T1-34).
- **Data the research step needs** (devlog 018), all public and bundleable in the page so no visitor data leaves it:
  - Census ACS by ZIP (ZCTA): median household income, median rent, home-ownership rate, language spoken at home, industry of employment → T2-A, T2-B, T2-C, T2-J
  - Industry wages by area (County Business Patterns, BLS) → T2-J
  - Phone model → release year table → T1-01 device age, T2-D timeframe
  - A source for the 2–4 year phone replacement cycle → T2-D
  - Smartphone-only internet use by income (e.g. Pew) → T2-B mobile path
  - GeoLite2 accuracy in the US → T1-31's "within X miles"
  - A fuller, checked provider table (US ISPs, campus, hospital, public Wi-Fi, VPN and hosting networks) → T1-32, T1-33, T2-G
  - Rural–urban codes by ZIP (USDA RUCA) → possible input
- **Keyboard layout** (`navigator.keyboard.getLayoutMap()`, Chromium): independent support for T2-C/T2-D.
- **Global Privacy Control** (`navigator.globalPrivacyControl`): a modern counterpart to Do Not Track for group P.
