# Claude Devlog

A record of each instruction exchange. Newest entries at the bottom.

**Entry format**
- **Instruction:** the user's request, as given
- **Interpretations:** each version reflected back, with the user's corrections
- **Confirmed intent:** the version the user approved
- **Executed:** what was changed
- **Verdict:** whether the result fulfilled the intent, in the user's words

---

## 001 · Set up devlog and working agreement
**Date:** 2026-11-24

**Instruction:** Create `claude-devlog`. For instructions (prompts that change the codebase), reflect back the believed intention, iterate until confirmed, then execute, then ask whether the execution fulfilled the intention. Keep a record of these exchanges in the devlog. (The user mentioned a second document as well.)

**Interpretations:**
1. Create the devlog, adopt the reflect → confirm → execute → verify loop, and log each exchange; questions are answered normally. Open points: should the workflow persist across sessions (via `CLAUDE.md`)? Should earlier work be logged? What is the second document?
   - *Correction:* the workflow should continue for this project specifically, and it may be appropriate to put it in a `CLAUDE.md` for this folder.
2. Added: create a folder-specific `CLAUDE.md` holding the workflow. Still open: logging earlier work, and the second document.
   - *Correction:* no need to log earlier work. The second document becomes a section of `CLAUDE.md` that records learnings from the devlog (actions that reliably fulfill intent, recurring misinterpretations, changes that reliably land) so the system improves over time.
3. Full version including the Learnings section, updated only when patterns emerge, with the user told when it changes.
   - *Confirmed.*

**Confirmed intent:** Create `claude-devlog.md` (starting with this exchange) and a folder-specific `CLAUDE.md` containing the instruction workflow and a Learnings section distilled from the devlog.

**Executed:**
- Created `CLAUDE.md` with Workflow, Files and Learnings (empty) sections
- Created `claude-devlog.md` with the entry format and this entry

**Verdict:** Fulfilled. "This is great." Pre-log preferences (e.g. disliking the bulky first `PRODUCT.md`) are not to be backfilled into Learnings; the process begins from here.

---

## 002 · Data collection audit
**Date:** 2026-11-24

**Instruction:** "I'd like an audit of what pieces of data we may be able to collect from site visitors."

**Interpretations:**
1. A research document, not code changes: inventory what `index.html` already collects, then catalogue what else is possible (passive, behavioral, network/IP, permission-gated), with what each reveals, possible inferences, and browser reliability. Output to a new `data-audit.md`; group promising items into `PRODUCT.md` only after the user reviews. Defaults: include permission-gated data in its own section; include outside-service options, flagged.
   - *Confirmed.* ("Yes let's do that.")

**Confirmed intent:** Interpretation 1, with both defaults.

**Executed:**
- Created `data-audit.md` with six sections: already collected, passive, behavioral, network (outside service), permission-gated, and "no longer possible"
- Flagged sensitive inferences (⚑) and browser reliability per item
- `PRODUCT.md` not changed, pending review

**Verdict:** Fulfilled. "The audit from 002 did what I intended." (Given after a follow-up in entry 003; the user had moved straight to the next instruction.)

---

## 003 · Collection infrastructure for audit categories 2 and 3
**Date:** 2026-11-24

**Instruction:** "I want to add the ability to collect and reflect data for everything in categories 2 and 3. We don't need to add any interpretation for now, just the mechanisms to collect and reflect."

**Interpretations:**
1. Add collectors for all passive (§2) and behavioral (§3) items and *show raw values on the page*: new rows in the Part One signal table, a live "your behavior on this page" block, and specific live-log messages. No interpretation text. No new data to GA. Also correct the audit's "already collected" section, which missed several existing collectors. Verify in a headless browser.
   - *Correction:* don't show anything. Set up collection infrastructure only; how data is reflected to the user comes later. Nothing a visitor sees should change.
2. Collection only, stored in code for later use; existing collectors upgraded in separate fields so displayed values stay identical; new collectors run after page load so nothing slows; no GA; no visible changes; audit §1 corrected; headless verification.
   - *Correction:* no verification needed now; QA comes later with prototyping and testing.
   - *Confirmed* otherwise.

**Confirmed intent:** Interpretation 2 without the verification step.

**Executed:**
- `index.html`: new "Extended collection" block after the existing orchestration.
  - `X` (passive, filled after `load`): client hints, WebGL params + unmasked renderer, WebGPU adapter, locale details + time zone/language pair, 16 extra media queries, screen/window geometry + multi-monitor, storage quota, Bluetooth availability, `webdriver`, 74-font list, full voice list, double canvas and audio reads (randomization check), GA script load check, navigation timing, CPU benchmark. `X.ready` flips to true when done.
  - `B` (behavioral, live): visit time, mouse distance/speed/pauses, scroll distance/speed/reversals, reading time per section, idle periods, tab hides/away time/blurs, selected and copied text (capped), resizes, touch points with force, device motion where no permission is needed.
  - No DOM, log or GA changes. Existing `S` values untouched.
- `data-audit.md` §1 rewritten to match what the page actually collected, noting the WebGL bar is hard-coded.
- Ran a syntax check only (no behavior testing, per instruction).

**Verdict:** Fulfilled. ("Let's mark this as yes.")

---

## 004 · Fix the canvas fingerprint
**Date:** 2026-11-24

**Context:** Asked to explain the canvas issue flagged in 003. While explaining, I corrected my earlier claim: the stored 32-character value still contained two image checksums, so it did work as a fingerprint. The real problem was the *visible* token (the last 8 characters), which fell entirely inside the PNG end-of-file marker and could only ever be one of three strings for every visitor.

**Instruction:** "Let's go ahead and fix."

**Interpretations:**
1. `S.canvas` becomes a hash of the full image; the signal table shows the first 8 characters of the hash; the existing "blocked" checks keep working; nothing else changes. Offered as optional extras: hashing raw pixels, replacing the hard-coded 94% bar, revisiting the "persists across incognito mode" log line.
   - *User asked a question* (benefits of raw pixels vs. PNG), then: no raw pixels; do include the bar and the incognito line.
2. Added: bar options (A: based on what was measured this visit; B: a cited research figure; C: both), recommending A. Log line: show only what was observed (token or blocked), drop the incognito claim.
   - *Confirmed*, with both recommendations, plus: "make a note to come back to this later in PRODUCT.md."

**Confirmed intent:** Interpretation 2 with option A, plus a note in `PRODUCT.md`.

**Executed:**
- `getCanvasFingerprint()` now reads the canvas twice via `xCanvasRead()`, stores the full-image hash in `S.canvas`, and sets `S.canvasStable`
- `xHash` output padded to 14 characters so tokens are a consistent length
- Signal table token: `Captured (token: <first 8 chars>…)`
- Canvas bar: 94 if stable, 25 if it varies between reads, 5 if blocked (other bars untouched)
- Log line replaced with the observed result for canvas (token or blocked) and audio (signature or blocked); incognito claim removed
- `PRODUCT.md`: follow-up note under "Showing it with impact"
- `data-audit.md` §1: noted the fix
- Syntax check only

**Verdict:** Fulfilled. ("Yes")

---

## 005 · GA synopsis and outgoing-events sidebar in PRODUCT.md
**Date:** 2026-11-24

**Context:** The user asked (as questions) whether GA is set up and what GA could collect, with the condition that it only matters if it can be reflected back to the user in real time. My answer: GA's inferences can't come back per user; the realtime API gives totals only and needs a server; per-user, real-time reflection is limited to what the page itself sends (GA cookies, outgoing request contents).

**Instruction:** Don't add the full overview to `PRODUCT.md`. Condense it into a synopsis (individual data can't be reflected back through GA), and add an item: a persistent sidebar that shows when events are sent to GA or other third parties, to circle back to later.

**Interpretations:**
1. Two short additions under "Showing it with impact": a 2–3 line GA synopsis and the sidebar item. Excluded: the detailed breakdown, sending `S`/`X`/`B` to GA, any code changes. Asked: should the sidebar reference the existing live log panel?
   - *Answer:* it stands on its own for now. *Confirmed.*

**Confirmed intent:** Interpretation 1, with the sidebar as a standalone item.

**Executed:**
- `PRODUCT.md`: added the GA synopsis and the sidebar item under "Showing it with impact"

**Verdict:** Fulfilled. ("Yes thank you")

---

## 006 · Cookie summary in PRODUCT.md
**Date:** 2026-11-24

**Context:** The user asked (as a question) what cookies can reveal and whether it can be reflected back. My answer: cookies are memory, not sensing; the page can read only its own cookies (including GA's `_ga`); they're valuable for history across visits, shown back on the same browser. I also noted the Part Two "four cookies" claim is wrong (GA adds at least two).

**Instruction:** "Add a summary to product.md."

**Interpretations:**
1. A one-to-two line summary under "What we can learn", matching the GA synopsis style. Excluded: the full table, the page observations, code changes. Asked: should the "four cookies" error go in as a separate fix-later item?
   - *Confirmed*, and yes, add the fix-later item.

**Confirmed intent:** Interpretation 1, plus a separate fix-later item for the cookie count.

**Executed:**
- `PRODUCT.md`: cookie summary under "What we can learn"; fix-later item for the "four cookies" claim under "Tone" (the section about the site's wording)

**Verdict:** Fulfilled. ("Yes thank you.")

---

## 007 · Simulated tracking pixels in PRODUCT.md
**Date:** 2026-11-24

**Context:** The user asked (as a question) what data pixels collect and whether it can be reflected back. My answer: pixels link visits to real platform accounts; that linkage stays on the platform's side; only what's sent (`_fbp`, `_fbc`, request payloads) is visible, and only if a real pixel is installed. I also found the Part Two "Meta Pixel" panel shows simulated data as real.

**Instruction:** Add this to `PRODUCT.md` with notes: show users what different pixels collect and send, without connecting real pixels to the site, via a clearly labeled simulation.

**Interpretations:**
1. Three additions split across sections: a pixel summary under "What we can learn," the simulation direction under "Showing it with impact," and a fix-later item for the Meta panel. Asked: should the simulation use the visitor's real data?
   - *Correction:* undecided on real data, so note it as open. Also note that conveying account linkage is a goal, with the method undecided. **Don't split across sections; keep features grouped together.**
2. One grouped item under "Showing it with impact" holding the description, the no-real-pixels and labeling constraints, two open questions (how to convey account linkage; real vs. generic data), the sidebar link, and the Meta panel fix. Plus a Learnings entry on grouping.
   - *Confirmed.*

**Confirmed intent:** Interpretation 2.

**Executed:**
- `PRODUCT.md`: "Simulated tracking pixels" grouped item under "Showing it with impact"
- `CLAUDE.md`: Learnings entry, keep a feature's notes grouped together

**Verdict:** Fulfilled. ("It did.") Earlier ungrouped items (cookie summary and its fix-later note; GA synopsis and sidebar) are to be left as they are; the grouping rule applies going forward.

---

## 008 · Inference map: engine review and rule design
**Date:** 2026-11-24

**Instruction:** Review the data collection components and devise what can be inferred from combinations, especially demographics (ethnicity, gender, sexuality), socioeconomic status and background vulnerabilities. Above all truthful; where it's a guess or likelihood, be clear. Impact comes from accurate reflection, not alarming language. Also suggest ways to test combinations.

**Interpretations:**
1. `inference-map.md` with a fixed confidence scale and sources; "can't infer" stated openly (e.g. gender/sexuality, with how platforms would infer it); 20 test profiles; no code. Flagged that the existing engine fails the standard (time zone as city, Mac/design-font misfire, unsourced claims). Asked: approach for unanswerable categories, review existing engine, sourcing. Offered six testing methods.
   - *Correction:* review the existing engine (keep/correct/cut). **Say nothing where nothing can be inferred**; the exception is conspicuous absence from VPN/privacy tools. Display likely as statements, sources at the bottom; confidence not shown per claim. Start with 20 profiles.
2. Engine review + rules (internal confidence/sources) + 20 profiles with hand-worked output.
   - *Correction:* second thoughts on profiles. The user distinguished piecemeal rules (1–2 signals → a sentence) from matching all data to a profile, and wanted links to real-world statistics. I clarified that the "profiles" were test visitors, gave the trade-offs, recommended piecemeal rules + an assembly step, proposed "statements about how data is used, not who the person is," and flagged that time zone ≠ city.
3. Modular rule catalog + real-world facts + assembly notes; test visitors deferred.
   - *Correction:* add threshold conclusions for bolder claims. **No "how data is used" statements**: Part Three covers that; this section reflects back to the user only, and bolder is more powerful. Disregard the DC/Minneapolis examples.
4. Engine review; Tier 1 rules about the person; Tier 2 threshold conclusions counting independent evidence only; assembly notes; tests deferred.
   - *User asked a question:* how to reduce research. Answer: no new web research, `[STAT]` placeholders, source status per claim, provisional confidence, a smaller first set; plus a `PRODUCT.md` line for a later research step.
   - *Confirmed.*

**Confirmed intent:** Interpretation 4 with the reduced-research approach.

**Executed:**
- Created `inference-map.md`:
  - Part 1: 8 cross-cutting problems + claim-by-claim keep/correct/cut table (49 claims: 30 cut, 17 correct, 2 keep). Found factual errors: Texas privacy law claim, P3 "almost exclusively Apple," "connection type identifies ISP," 10.x = corporate
  - Part 2: 29 Tier 1 rules in 8 evidence groups
  - Part 3: 6 Tier 2 conclusions (income, tight budget, language community/ethnicity, device from abroad, disability, actively hiding)
  - Parts 4–5: assembly notes; signal gaps (IP location, keyboard layout, GPC)
  - No gender/sexuality rules (no supporting signal)
- `PRODUCT.md`: grouped "Inference engine" item under "What we can learn", with the research step and test visitors as later items
- No web research; no code changes

**Verdict:** Fulfilled. ("Yes, thank you.")

---

## 009 · Apply the engine review to `buildProfile()`
**Date:** 2026-11-24

**Instruction:** "Please go ahead and implement the adjustments you'd make to the existing engine."

**Interpretations:**
1. Apply `inference-map.md` Part 1 to `index.html`: remove cut claims; rewrite corrected ones with the map's replacement wording, limited to replacing existing claims; no `[STAT]` clauses; kept claims stay; `renderProfile()` skips empty sections. Excluded: new Tier 1 rules, Tier 2, case scoring, styling. Asked: (1) fix the summary row too (city from time zone, "Local IP")? (2) remove the UK GDPR line from the profile and note it in `PRODUCT.md`, or leave it?
   - *Answers:* (1) make the corrections needed for accuracy; (2) remove it from the profile. *Confirmed.*

**Confirmed intent:** Interpretation 1, with the summary row corrected and the GDPR line removed.

**Executed:**
- `index.html`: rewrote `buildProfile()` / `renderProfile()`; added helpers `profileZoneName()` and `profileLanguageNames()`
  - Location → time-zone name (e.g. "Central Time") + languages in order of preference
  - Data plan → Save-Data statement only
  - Software → programming fonts (Fira Code, JetBrains Mono) and fonts "distributed with Adobe software" (deliberately not claiming a Creative Cloud subscription, since Reader may install some of them)
  - Fingerprint → blocked / randomized / captured (with token), plus what the ad blocker did not stop
  - Time → observed device type and local time
  - Referrer → observed
  - Network and political sections removed entirely (everything in them was cut)
  - Summary row: "Location" → "Time zone" (zone name); Fingerprint shows "Randomized" when reads differ; Local IP removed
  - Still computes `isUStz` / `isSouthernUS` / `isEUtz` for `renderCases()` (unchanged; flagged in the map)
- `inference-map.md`: Part 1 marked implemented
- Syntax check, plus the engine run on two sample visitors to check wording; fixed two grammar issues it showed
- Not noted in `PRODUCT.md`: the GDPR line (removed only, per the answer)

**Verdict:** Fulfilled. ("Yes")

---

## 010 · Build the full engine and run the test visitors
**Date:** 2026-11-24

**Instruction:** "I'd like to run tests using the profiles approach you suggested." Then: "Go ahead and implement all of your suggestions in the inference-map and what we've discussed already in index.html. Then we can run the test profiles against working code."

**Interpretations:**
1. 20 test visitors (`tests/personas.json`), a Node runner extracting the engine from `index.html`, results in `tests/results.md`; no `index.html` changes. Asked: test only the current (Part 1) engine, or also hand-work Tier 1/2?
   - *Redirect:* build everything in the map into `index.html` first, then test against working code.
2. Engine as a pure function separate from rendering; all 29 Tier 1 rules, 6 Tier 2 conclusions, assembly (absorb, one per topic, conflict, weak cap ≤2, ordering); profile renders immediately, re-renders when `X` is ready, live statements refresh; collect keyboard layout + GPC; summary row and bar unchanged. Defaults: omit `[STAT]` clauses (qualitative wording where confident); lookup-table rules show only the observed part; sensitive conclusions as drafted. Then personas/runner/results. Excluded: sidebar, pixels, tone pass, cookie-count fix, sources, case scoring.
   - *Confirmed.*

**Confirmed intent:** Interpretation 2.

**Executed:**
- `index.html`:
  - New inference engine (`inferProfile`, `inferLive` + helpers) between `INFERENCE ENGINE START/END` markers. Pure: reads only its argument
  - Page glue: `buildProfile` / `renderProfile` / `refreshProfile`; statements escaped on render; live paragraph updated every 3 s
  - Collection: `xKeyboardLayout()` and `X.gpc`; `collectExtended()` calls `refreshProfile()` when done
- `tests/personas.json` (20 visitors, base + overrides), `tests/run-personas.js`, `tests/results.md`
- First run: all targets hit except p20's conflict (runner counted suppressed conclusions as missing; fixed the runner). Reading the output found wording bugs, all fixed: "a NVIDIA" / "a Intel(R)", driver details in GPU names, singular/plural fonts, Reader fonts listed as Creative Cloud, double punctuation after copied text, straight vs. curly apostrophes
- Final run: all targets hit; T1-14 never fires (explained in the map)
- `inference-map.md`: "Implementation notes" section listing deviations from the draft
- `PRODUCT.md`: test visitors item updated
- Not tested in a real browser

**Verdict:** Fulfilled. ("This was a successful launch of a test bed.") The user is collecting notes on the results to pass on.

---

## 011 · Remove Google Analytics; record GA events locally for simulation
**Date:** 2026-11-24

**Instruction:** Disable Google Analytics on the site; GA data will be simulated the same way as pixel data. Remove GA and add a note to simulate GA data in `PRODUCT.md`.

**Interpretations:**
1. Remove the GA tag and the four event calls plus the log mirror; fix text that becomes false (footer's GA claims; engine's "GA blocked" check, which would fire for everyone; the `_ga` "Google first assigned your browser an ID" statement); leave the Part Two GA panel and the 56% stat for the simulation pass. `PRODUCT.md`: fold GA into the simulated-pixels item, mark "four cookies" resolved. Update p05/p06 and re-run tests.
   - *Correction:* don't re-run tests (the user is still reviewing). Still simulate GA data being collected. *Question:* can we tell whether a user might have sent GA data without sending it to Google? Answer: what GA *would have collected*, yes, exactly, built locally; whether a blocker *would have stopped* it, only approximately (bait checks), because the only sure test contacts Google.
2. Keep the event hooks pointed at a local recorder (GA4-shaped, sends nothing, shows nothing); leave `tests/` untouched; add the blocking likelihood with a same-site bait file to the note.
   - *Correction:* simulate only what the site would attempt; sidestep blocking. Suggested wording "when you arrived the site sent [data types] to Google." I flagged that "sent" would be untrue now and proposed "would have sent" with a simulation label, per the earlier rules.
3. Final: as 2 without blocking; wording note in `PRODUCT.md`.
   - *Confirmed.* ("All this sounds good.")

**Confirmed intent:** Interpretation 3.

**Executed:**
- `index.html`:
  - Removed the `gtag.js` tag and `G-7JHYWRBJ4Z` config from `<head>`
  - Replaced the `window.gtag` wrapper (and its "GA ·" log lines) with a local `gtag()` that records events into `simGA.events`, shaped as GA4 requests (v, tid, cid, sid, sct, en, ep, dl, dr, dt, sr, ul, _et). Simulated client ID in memory only; no cookie, no network request. Also records GA4's automatic `first_visit` / `session_start` / `page_view`
  - Existing event calls (scroll depth, clicks, sections viewed, time on page) unchanged; they now hit the recorder
  - Footer: removed "Behavioral data is transmitted to Google Analytics" and "GA4"
  - Engine: removed the GA-blocked variant of T1-20 and the `_ga` sentence of T1-26; collection: removed the GA load check and the 1.5 s wait it needed
- `PRODUCT.md`: "Simulated tracking pixels" → "Simulated third-party tracking (pixels and GA)" with the GA notes, wording note, an open question on persisting the simulated ID, and the Part Two GA panel fix-later; "four cookies" marked resolved
- `inference-map.md`: implementation note on T1-20/T1-26 and the p05/p06 test update needed
- `tests/` not edited or run, per instruction. Syntax check only

**Verdict:** Fulfilled. ("Yes, this did as intended.") Open question answered: it's fine that the simulated GA ID resets on every reload.

---

## 012 · Single-session rule for recorders; bring PRODUCT.md up to date
**Date:** 2026-11-24

**Instruction:** Local recorders shouldn't keep any recorded data outside a single session. Update `PRODUCT.md` so it's current and consistent with progress so far.

**Interpretations:**
1. The recorders (`simGA`, `B`, `X`) already live in page memory only, so: write the rule down (`CLAUDE.md` "Project rules" + a code comment). Asked whether it covers the page's own cookies (three outlast a session); default: recorders only. `PRODUCT.md`: update "Built so far," mark the engine built, note GA removal on the GA synopsis and sidebar, drop `_ga` references from the cookie summary, mark the simulated-ID question answered; leave older ungrouped items.
   - *Confirmed*, plus: add a note to revisit cookies in `PRODUCT.md`.

**Confirmed intent:** Interpretation 1 (rule covers recorders only), plus a cookies revisit note.

**Executed:**
- `CLAUDE.md`: new "Project rules" section with the single-session rule for recorders
- `index.html`: rule comment above the recorders (no behavior change)
- `PRODUCT.md`: "Built so far" rewritten; engine marked built (and `[STAT]` omission noted); p05/p06 test note; cookie summary without `_ga`, plus a "Revisit" sub-item on the page's persistent cookies vs. the rule; GA synopsis and sidebar note GA's removal; simulated-ID question marked decided; Tone notes the profile rewrite already done. Within the simulated tracking item, the GA bullets now come before the pixel bullets

**Verdict:** Fulfilled. ("Yes")

---

## 013 · Test-bed notes: drop unavailable data, add explication, note the impact goal
**Date:** 2026-11-24

**Instruction (pasted notes):** (1) Don't tell the user when data wasn't available, e.g. "your search terms weren't passed," "you arrived without a referrer." (2) Say more about what the information means; reworded fingerprint example given. (3) The language was dialed back too far: make inferences bolder and language more impactful (two examples given). Note (3) and circle back after the research step: levers are inference insight (after research) and language (later if needed).

**Interpretations:**
1. Now: remove the no-referrer statement and the search-terms clause (privacy-tool "conspicuous absence" statements stay); replace the fingerprint paragraph with the user's wording. Later: a grouped `PRODUCT.md` note with both levers, the examples, and a flag that "in the last 4 years" has no supporting signal. Then update p05/p06 and re-run tests. Asked: fold the audio fingerprint in or drop it? Apply (2) beyond the fingerprint now or defer?
   - *Answers:* don't run tests yet. Apply (2) generally where relevant: "we're at 1 out of 10 for explication; raise it to 3." (Audio question not answered; folded in by default, reversible.)
   - *Confirmed.*

**Confirmed intent:** Interpretation 1 with explication applied broadly at a modest level; no test runs.

**Executed:**
- `index.html` engine:
  - Removed the no-referrer statement and "your search terms weren't passed to this page"
  - T1-21 fingerprint → the user's wording; the silent sound folded in ("…and does the same with a silent sound"); the audio-only sentence kept for when the canvas is blocked or randomized
  - One short meaning clause added to T1-01, 03, 06, 09, 10, 11, 15/16, 20, 23, 24, 26, 27 (live), 28, 29, 30. Statements that already carried meaning (T1-02, 04, 05, 07, 08, 13, 17, 18, 22, 25, Tier 2) unchanged
- `PRODUCT.md`: explication note; "Increase impact" item with both levers, the user's examples verbatim, and the research flag
- `inference-map.md`: implementation note
- `tests/` not edited or run, per instruction. Syntax check only

**Verdict:** Fulfilled. ("Yes")

---

## 014 · Hand-off note for the next session
**Date:** 2026-11-24

**Instruction:** The user is stepping away; the project resumes in a new context window. Leave a note that the next items are: set up IP address collection and reflection, then feed that data into the inference engine, then deep research to build out the engine with more insight.

**Interpretations:**
1. A "Next up" section in `CLAUDE.md` (read automatically) with the three steps in order, orientation pointers, and the decisions step 1 raises (outside lookup service vs. the footer's "local" claim; single-session rule). Matching next-steps line in `PRODUCT.md`.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `CLAUDE.md`: "Next up" section (orientation reading list, three ordered steps, decisions to settle, pending p05/p06 test update)
- `PRODUCT.md`: "Next steps" line at the top of "What we can learn"

**Verdict:** Fulfilled. ("Yes", given 2026-11-25)

---

## 015 · Collect the visitor's IP address (collection only)
**Date:** 2026-11-25

**Instruction:** Start "Next up" step 1. Collect IP addresses even though it means a third party; set up the collection infrastructure in the working version, with reflection later.

**Interpretations:**
1. New page-memory recorder for network data; one lookup on load; store IP, location, provider; nothing displayed, engine and tests untouched. Notes in `PRODUCT.md` (footer claim now untrue, VPN detection not covered) and `data-audit.md` §4. Asked: which service (recommended ipapi.co), and change the footer now or note it?
   - *Answers:* remove the footer's "processing is local" line. Asked whether ipapi.co needs an account, and whether the IP could be processed on-site instead. (Answered: finding the IP always needs an outside party or our own server; interpreting it can be local with a downloadable database; options (a) ipapi.co, (b) STUN plus local database, (c) server function at the host.)
2. Same, with the footer line removed and option (a).
   - *Correction:* use (a) ipapi.co, but note in `PRODUCT.md` that we switch to (c) when the site is hosted publicly.
3. Checked ipapi.co's terms; corrected my earlier claim (it's a ~100/day trial, not ~1,000/day, development only). It logs queried IPs and is a registered data broker.
   - *Correction:* look at other free services first. Sending visitor data to a data broker is unethical for a site about data exposure.
4. Researched 11 services against their own policies and the California data-broker registry. Recommended country.is (open source, no request logs, not a broker; data from MaxMind GeoLite2, looked up on its own server). No free service offers VPN/proxy/mobile detection.
   - *Confirmed.*

**Confirmed intent:** Interpretation 4: collection via country.is into a page-memory recorder, footer line removed, notes in `PRODUCT.md` and `data-audit.md`.

**Executed:**
- `index.html`:
  - New recorder `N` (after the `X` collector): one `fetch` to `https://api.country.is/?fields=city,continent,subdivision,postal,location,asn` on load, no cookies or referrer sent, 8 s timeout
  - Stores raw response plus ip, ipVersion, country, continent, region, city, postal, latitude, longitude, accuracyKm, timeZone, asn, org; `status` = ok / rate-limited / no-data / error / timeout / blocked
  - Project-rule comment now names `N`
  - Footer: "All browser-based processing on this page is local to your device" removed; remaining line's top margin dropped
- `PRODUCT.md`: "IP address" item (service choice and why, switch to (c) when public, VPN detection gap, footer change)
- `data-audit.md` §4: collected items marked ✓
- Not changed: display, inference engine, tests. Syntax check only; the live call was not run

**Follow-up (not an instruction, no files changed):** the user asked for a test. Ran the page's `collectNetwork()` in Node against the live service: status ok, all fields returned (subdivision, continent and postal are plain strings; region and continent are codes, e.g. "NY"). CORS confirmed (`access-control-allow-origin: *`). Not tested in a real browser with extensions.

**Verdict:** Fulfilled. ("Yes")

---

## 016 · Bring hand-off notes up to date; third-party rule
**Date:** 2026-11-25

**Instruction:** Update the stale notes (`CLAUDE.md` "Next up", `inference-map.md` Part 5) and add the note about checking third parties against the project's purpose.

**Interpretations:**
1. Third-party guidance in `CLAUDE.md` (asked: Learnings or Project rules?); "Next up" step 1 marked collected, reflection still open; Part 5 gap updated; devlog entry.
   - *Answer:* Project rules. *Confirmed* otherwise.

**Confirmed intent:** Interpretation 1, with the guidance as a project rule.

**Executed:**
- `CLAUDE.md`: new project rule "Check outside services against the project's purpose"; "Next up" step 1 now shows collection done (devlog 015) and reflection still to do
- `inference-map.md` Part 5: city-level location gap now says collected in `N`, not yet used by the engine

**Verdict:** Fulfilled. ("Yes")

---

## 017 · Feed IP data into the inference engine
**Date:** 2026-11-25

**Instruction:** Move to "Next up" step 2.

**Interpretations:**
1. Design IP rules in `inference-map.md` (new evidence group I; city-level location replacing T1-10; internet provider with type; VPN/data-center guess feeding T2-F; clock and language mismatches against the connection), flag ZIP-level Census data for research, notes, no tests. Asked: design first or design and build together? Should built IP statements appear on the page now?
   - *Answers:* design and build together; showing it on the page is fine for now. *Confirmed.*

**Confirmed intent:** Interpretation 1, designed and built, shown in the profile.

**Executed:**
- `index.html` engine:
  - New input `D.N`; provider table `ENG_PROVIDERS`, US state names, `engProvider`, `engOrgName`, `engUtcOffset`
  - T1-31 location from IP (replaces T1-10 when it fires); T1-32 internet provider by type; T1-33 VPN/data center (group P, blocking, feeds T2-F); T1-34 clock vs. connection time zone
  - T1-12 and `isUS` use the connection country when known (not when a VPN is detected)
  - T2-F "still visible" list adds approximate location and provider
- `index.html` glue: `engineInput()` passes `N`; profile re-renders when the lookup finishes
- `inference-map.md`: group I, rules T1-31 to T1-34, T1-12 update, implementation note, Part 5 updated (ZIP demographics gap added)
- `PRODUCT.md`: one line under "IP address"
- Not changed: tests (not edited or run). Syntax check only

**Verdict:** Fulfilled. ("Yes")
