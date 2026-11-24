# Inference Map (draft for engine review)

**Status: provisional.** Built without new research (devlog 008). Nothing here is ready to display until the research step fills in the sources and the `[STAT: …]` placeholders.

**Standards**
- Reflect back what we *know* about the visitor. If something can't be inferred, say nothing. The exception is **conspicuous absence**: privacy tools that hide or fake data.
- Accuracy over alarm. Where it's a likelihood, the statement says so.
- Confidence and sources are tracked here for review. Display is decided later (sources likely at the bottom of the page).
- Gender and sexuality are absent on purpose: no collected signal supports a truthful inference.

**Keys**
- **Confidence (provisional):** **Obs** = observed fact · **Strong** = well documented, few exceptions · **Likely** = real correlation, many exceptions · **Weak** = pattern used by industry, often wrong
- **Source:** ✓ = known source, named (still to be checked in research) · **?** = needs source · *n/a* = observed, no source needed
- **⚑** = sensitive (health, disability, finances, origin)

---

## Part 1: Review of the existing engine (`buildProfile()`)

**Implemented in devlog 009.** Cuts removed; corrections rewritten as observed statements, with no `[STAT]` clauses. The UK GDPR line was removed from the profile. The summary row now shows the time-zone name, labels randomized fingerprints, and no longer shows Local IP. `renderCases()` is unchanged.

### Problems that affect many claims
1. **Time zones are not cities.** Browsers return IANA zone names like `America/New_York` and `America/Chicago`. `America/New_York` covers the entire US Eastern zone, so anyone in Miami, Atlanta or DC is told "your timezone places you in New York." Most `tzMap` keys (`San_Francisco`, `Seattle`, `Austin`, `Dallas`, `Boston`…) are not IANA zone names, so they never match. ✓ IANA tz database
2. **The WebRTC local IP is almost never available.** Chrome, Safari and Firefox now hide local addresses behind random `.local` names, and with `iceServers:[]` there is no public address either. Every branch that depends on `localIP` is effectively dead. ✓ WebRTC mDNS candidate obfuscation (Chrome 2019+, others since)
3. **"10.x means a corporate network" is wrong.** Many home routers use 10.x, for example Comcast/Xfinity gateways (10.0.0.1). ✓
4. **The font flags misfire.** Futura, Avenir and Gill Sans come preinstalled on macOS, so `hasDesignFonts` is true for every Mac. Franklin Gothic Medium ships with Windows, so `hasOfficeFonts` is true for nearly every Windows PC. ✓ Apple and Microsoft font lists
5. **`navigator.connection.downlink` is capped at 10 Mbps in Chrome,** so the `downlink>20` ("fiber") branch never fires. ✓ Network Information API / Chrome implementation
6. **Camera and mic counts are limited before permission.** Before the visitor grants access, browsers report at most one device of each kind, so `cameras>1 && mics>1` almost never fires. ✓ W3C Media Capture spec
7. **`canvas === 'blocked'` rarely happens.** Privacy browsers randomize canvas output rather than throwing errors. The real signal is `S.canvasStable === false` (added in 004).
8. **Several narratives describe how data is used,** for example sales to insurers and law enforcement access. That belongs in Part Three, not in the reflection.

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

**Evidence groups:** **D** device hardware · **W** installed software (fonts, voices) · **L** locale settings (language, region, clock format) · **Z** time zone · **T** time and behavior · **P** privacy tools · **A** accessibility settings · **H** visit history and referrer

### Device and spending

**T1-01 · Phone model** · D · Obs (model) / Strong (year)
- Trigger: `X.clientHints.model` (Android, Chromium only)
- "Your phone is a [Samsung Galaxy A15], released in [2023]."
- Source: *n/a* for the model; ? needs a model → release-year table

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
- Trigger: `S.tz`
- "Your clock is set to [US Central] time."
- Source: *n/a* (replaces all city claims)

**T1-11 · Languages** · L · Obs
- Trigger: `S.langs` with more than one distinct base language
- "Your browser is set to [Spanish] first, then [English]."
- Source: *n/a*. Note: devices bought in the US default to English, so a non-English *first* language was usually chosen deliberately or the device was set up elsewhere

**T1-12 · Language region vs. time zone** · L+Z · Likely ⚑
- Trigger: `X.locale.tzLangPair.languageRegion` ≠ the country of `S.tz` (e.g. `es-MX` in `America/Chicago`)
- "Your browser's language is set for [Mexico], and your clock is set to [US Central] time. About [STAT: number] people in the US speak [Spanish] at home."
- Source: ✓ US Census Bureau, American Community Survey (language spoken at home)

**T1-13 · Installed voices for other languages** · W · Likely ⚑
- Trigger: `X.voicesList` entries with `local: true` in languages other than the system language
- "Your device has text-to-speech voices installed for [Korean] and [Hindi], which usually come from language packs added by the owner."
- Source: ? Note: Chrome on desktop adds ~20 network "Google" voices in many languages; count only `local: true`

**T1-14 · Clock format** · L · Weak
- Trigger: `X.locale.hourCycle` = h23 in a US time zone with an `en-US` locale
- "Your device shows time in 24-hour format, which is uncommon in the US."
- Source: ? Note: depends on locale; only meaningful when the locale is `en-US`

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

**T1-20 · Analytics blocked** · P · Obs
- Trigger: `X.tracker.gaScriptLoaded === false`
- "Your browser blocked this page's Google Analytics. The events in the log never reached Google."
- Source: *n/a*

**T1-21 · Fingerprint randomized** · P · Strong
- Trigger: `S.canvasStable === false` or `X.audioCheck.stable === false`
- "Asked to draw the same image twice, your browser gave two different answers. It is disguising your device."
- Source: ✓ Brave and Firefox fingerprinting-protection docs

**T1-22 · Tor or strict-protection pattern** · P · Strong
- Trigger: `S.tz` = UTC + `en-US` + window size in 200×100 steps (letterboxing) and/or randomized canvas
- "Your browser reports UTC time and a rounded window size, the pattern produced by Tor Browser's protections."
- Source: ✓ Tor Browser design document (letterboxing, UTC)

**T1-23 · Do Not Track** · P · Obs
- Trigger: `S.dnt === '1'`
- "You've asked websites not to track you."
- Source: *n/a*

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
- Trigger: `vis` cookie > 1, `_ga` timestamp
- "This is your [3rd] visit. Google first assigned you an ID on this site on [date]."
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

**T2-A · Higher income** · Likely ⚑
- Requires: a premium device (T1-02, T1-03, T1-04 or T1-05) **and** at least one more independent group: paid professional software (T1-17, group W) or multiple monitors (T1-09 counts only if the device evidence is a phone)
- US time zone only (sources are US)
- "Your equipment and software are consistent with a household in the top [STAT: income band] in the US."
- Blocked by: T2-B (if both fire, show neither; show the Tier 1 facts)
- Source: ✓ Bertrand & Kamenica (2018) for the device part; ? for the combination

**T2-B · Tight budget** · Likely ⚑
- Requires: low-cost device (T1-07, group D) **and** data saver (T1-08) or out-of-support software (T1-06)
- Note: T1-06/07/08 are all in group D. For this conclusion, data saver is a *setting* the owner chose, so it counts as separate from hardware. Flagged for review
- "Your phone and settings suggest you're managing costs carefully: a [2 GB] phone with data saver turned on. [STAT: share of prepaid/limited-plan users by income]."
- Source: ?

**T2-C · Language community / likely ethnicity** · Likely ⚑
- Requires: US time zone + non-English first language (T1-11, group L) **and** independent support from group W (local voices or non-default fonts for that language/script, T1-13)
- "You most likely speak [Spanish] at home. Among people in the US who do, [STAT: share] identify as Hispanic or Latino."
- Applies only to languages where a Census figure exists
- Source: ✓ US Census Bureau, ACS

**T2-D · Device set up outside the US** · Likely ⚑
- Requires: language region ≠ time zone country (T1-12, groups L+Z) **and** a device-side signal from group D: an Android model code for a different region (e.g. Samsung's `F`/`B` international suffix vs. `U` for US carriers, from T1-01)
- "Your phone is an international model and your browser is set for [Mexico], while you're on US [Central] time. Your device was most likely bought or set up outside the US."
- Source: ? manufacturer model-code conventions

**T2-E · Disability** · Likely / Weak ⚑
- Requires: two different accessibility mechanisms (two of: forced colors, increased contrast, inverted colors, reading-support font T1-25), **or** one strong one (forced colors, inverted colors, OpenDyslexic). Reduced motion and dark mode alone don't count
- "Your settings indicate you likely have [low vision / dyslexia]."
- Source: ? usage rates of these settings among disabled vs. non-disabled users. **At risk of being cut** in the research step if no data exists

**T2-F · Actively hiding** · Strong
- Requires: two or more privacy signals from group P (T1-20 to T1-23)
- "You're actively hiding from trackers, and it's partly working. [What was blocked: analytics, fingerprint.] [What wasn't: time zone, screen, language, this visit's behavior.]"
- Source: *n/a* (observed). This is the conspicuous-absence exception

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
- **Devlog 013:** statements for unavailable data removed (no-referrer statement; "your search terms weren't passed"). Short "what this means" clauses added to T1-01, 03, 06, 09, 10, 11, 15/16, 20, 23, 24, 26, 27, 28, 29, 30. T1-21 rewritten in the user's wording, with the silent sound folded in.

## Part 5: Gaps noticed (not collected, would strengthen rules)

- **City-level location:** needs an IP lookup service (set aside earlier). Without it, location stays at time-zone region.
- **Keyboard layout** (`navigator.keyboard.getLayoutMap()`, Chromium): independent support for T2-C/T2-D.
- **Global Privacy Control** (`navigator.globalPrivacyControl`): a modern counterpart to Do Not Track for group P.
