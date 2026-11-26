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

---

## 018 · IP fixes, network types, new Tier 2, inference-map pass
**Date:** 2026-11-25

**Instruction:** (Following a review of new IP inferences, answered as a question.) Make the fixes the review highlighted, and consider pasted notes: sunset/daylight and area-only demographics aren't interesting (aim for "how did it know that" about the person; area data only as input, e.g. income band, rent, home ownership); network types and employer are very interesting; "last 4 years" ties to the 2–4 year phone replacement cycle (verify in research); cross-reference area industries with socioeconomic signals to infer industry.

**Interpretations:**
1. Four fixes in `index.html` (mobile → state only; T1-34 wording; privacy-relay variant; school districts split out). Notes recorded in `PRODUCT.md` for research, nothing built. Asked: record the review's candidates in `inference-map.md`? Build network-type/employer rules now or wait?
   - *Answers:* yes, take a pass through the whole of `inference-map.md` using the notes as guiding principles; update the provider table now as a quick pass.
2. Fixes; provider table quick pass (hospital, library, public Wi-Fi, military, prison tablets); full `inference-map.md` pass (principles in Standards, stale entries brought in line, area stats moved to inputs, device-age angle, Tier 2 reworked, dropped list, data needs); `PRODUCT.md` note; no Tier 2 building, research or tests.
   - *Correction:* build the new Tier 2 conclusions as well. *Confirmed* otherwise.

**Confirmed intent:** Interpretation 2, with new Tier 2 conclusions built.

**Executed:**
- `index.html` engine:
  - T1-31: mobile carriers get state/country only (no city, ZIP, miles)
  - T1-34: "usually a VPN or a clock set by hand; less often, you've just traveled"
  - Provider table: new types `relay` (Akamai/Cloudflare/Fastly), `school`, `hospital`, `library`, `public-wifi`, `military`, `corrections`, and a generic `isp` catch-all (so small ISPs aren't read as employers); "Universal" no longer matches universities. T1-32 wording for each
  - T1-33: separate privacy-relay wording ("keeps your rough region"); T2-F shows "your rough region" as still visible
  - T2-B: public library network counts as a cost signal
  - New, built: T2-G at work at [employer] (unrecognized org + weekday working hours on a computer); T2-H living on campus (university network + midnight–5 AM); T2-I away from home country (language + clock agree on one country, connection in another)
  - Not built (need data): T2-A location path, T2-C ZIP support, T2-D timeframe, T2-J likely industry. Marked Planned
- `inference-map.md`: full pass. Status line; three guiding principles in Standards; Part 1 problem 1 marked addressed; T1-01 device-age plan; T1-10/12/14 updated; T1-30 entry added; T1-20/23/26 brought in line with what's built; T1-31–34 rewritten; Tier 2 status key, T2-A–D updated, T2-F–J added; "considered and dropped" list; assembly note 9; implementation note; Part 5 data-needs list
- `PRODUCT.md`: guiding-principles line under "Increase impact"
- Not changed: tests (not edited or run). Syntax check only

**Verdict:** Fulfilled. ("Yes")

---

## 019 · Research plan
**Date:** 2026-11-25

**Instruction:** Skip updating test visitors until after research; begin to prep for research.

**Interpretations:**
1. A `research-plan.md` gathering claims to source, `[STAT]`s, datasets, questions to settle (incl. the user's examples) and priorities; "Next up" updated. Asked: separate file or part of `inference-map.md`?
   - *Answer:* new file `research-plan.md`. *Correction:* the user's examples are the *type* of inference wanted, not claims to verify. The research is mainly to discover what inferences the data supports, e.g. correlations found in sources; verification is secondary. Asked: research new data points too? count industry claims? include sensitive topics? geography?
   - *Answers:* (1) yes, new data collectable with current systems if meaningful; (2) yes, present industry claims flagged as advertisers'/data brokers' perspective; (3) include sensitive topics, decide case by case; (4) US only.
2. Plan organized around discovery: purpose, scope, where to look, evidence types and wording, data groups in priority order, findings log, secondary verification track, datasets, process.
   - *Correction:* widen "where to look" to sociological studies, polling and marketers' own published material; reliability can be traded for a caveat ("according to marketers"); reflecting how advertisers, marketers or bad actors would infer and target the visitor is itself interesting. *Confirmed* otherwise.

   - *Further corrections (after execution):* industry inferences are worth reporting even when unreliable; sometimes the unreliability is the point. Answer to the open question: targeting statements go in the profile when truthful, considered and impactful (example: not "advertisers know the sun is out"; yes "because you work in government you're three times more likely to receive lobbying ads"). Concern: researching by data group may miss cross-group inferences; asked for a more holistic approach.
3. Amend the plan: accuracy not required for industry inferences, "based on nothing more than" wording, profile test for targeting statements, three passes (A: start from the person's attributes; B: signal sweep; C: combine into a grid), log fields for reliability and groups combined; note in `inference-map.md`.
   - *Confirmed.*

**Confirmed intent:** Interpretation 3 (2 plus the amendments).

**Executed:**
- `research-plan.md` (new): purpose (discovery first; three perspectives: research, advertisers/brokers, bad actors), scope (US, all groups, new data, sensitive topics in), where to look, evidence types → wording table, data groups in order, findings log format, secondary track (`?` sources, ✓ to check, `[STAT]`s, open questions), datasets to bundle, empty findings section, process. Open question noted: targeting statements vs. `inference-map.md` problem 8 (usage belongs in Part Three)
- `CLAUDE.md` "Next up": step 2 marked done; step 3 points to `research-plan.md`; test-visitor update moved to after research
- Amendments: `research-plan.md` §1 (accuracy not required for industry inferences), §4 (weak-basis wording; profile test for targeting statements with the user's examples), §5 replaced with Passes A/B/C, §6 (groups-combined and reliability fields), §10 (pass order; open question removed). `inference-map.md`: Standards bullet on targeting statements; Part 1 problem 8 revision note

**Verdict:** Fulfilled. ("Yes")

---

## 020 · Research Pass A
**Date:** 2026-11-25

**Instruction:** Start Pass A (asked as "do you know what that means?").

**Interpretations:**
1. Research the six attribute areas, log findings in `research-plan.md` §9, check sources against the third-party rule, build nothing, no tests. Asked: all six areas at once or one at a time? Sub-agents?
   - *Correction:* the third-party rule doesn't apply to reference sources (they receive no visitor data). Use any source; reliability decides the wording, not inclusion.
2. As 1, using any source, plus §10 amended so the rule covers only services or new data points that receive visitor data.
   - *Answers:* all six areas in one go, without losing depth; no sub-agents (speed isn't a concern, token cost is). *Confirmed.*

**Confirmed intent:** Interpretation 2, done directly without sub-agents.

**Executed:**
- `research-plan.md` §10: third-party rule scoped to services or new data points that receive visitor data
- `research-plan.md` §9: Pass A findings A-01 to A-42 (numbers A-21 and A-38 unused) across the six areas, a strongest-candidates list, areas with no supportable signal, and a list of figures to check in primary sources. Status line updated
- Not changed: `index.html`, `inference-map.md`, tests

**Verdict:** Fulfilled. ("Yes this is good. The types of inferences you uncovered are generally interesting.")

---

## 021 · Minors
**Date:** 2026-11-25

**Instruction:** Don't handle minors: no collecting or reflecting distinguishing information about people under 18. Note in `PRODUCT.md` that visitors should be told this was an option we chose not to take, and given information on what data might be collected about minors and how it's used.

**Interpretations:**
1. One short `PRODUCT.md` item (decision; show visitors the option declined and examples from Pass A). Nothing built. Asked: flag the school-district network type and the A-15 teen guard for review? Mark A-16 declined in `research-plan.md`?
   - *Answers:* note right as written. Stating a school-district network is fine; flagging a likely teen only to withhold income readings is fine. Remove A-16 so it isn't proposed again. *Confirmed.*

**Confirmed intent:** Interpretation 1, plus A-16 removed.

**Executed:**
- `PRODUCT.md`: "Minors" item under "What we can learn", including the two allowed cases
- `research-plan.md`: A-16 replaced by a one-line "Declined, don't propose again" stub; removed from the strongest-candidates list and from A-18's cross-reference
- Not changed: `index.html`, `inference-map.md`, tests

**Verdict:** Fulfilled. ("yes")

---

## 022 · Research Pass B
**Date:** 2026-11-25

**Instruction:** Go ahead with Pass B.

**Interpretations:**
1. Sweep the eight data groups in §5 order (I, D, T, L/Z, W, P, A, H) for documented links, alone or across groups; log as B-01 onward in §9, cross-referencing Pass A rather than repeating; note predictive new data; same rules as Pass A (any source, nothing about minors, no sub-agents, all groups in one go). No changes to `index.html`, `inference-map.md` or tests.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `research-plan.md` §9: Pass B findings B-01 to B-27 (B-13 unused; battery points to A-40), with strongest candidates, guards on existing rules (fiber, Android), secondary-track sources, and a to-check list. Status line updated
- Not changed: `index.html`, `inference-map.md`, tests

**Verdict:** Fulfilled. ("yes")

---

## 023 · Research Pass C
**Date:** 2026-11-25

**Instruction:** Start Pass C.

**Interpretations:**
1. Build an attribute × evidence-group grid in §9 from Pass A, Pass B and existing rules; any attribute predicted by two or more independent groups becomes a candidate Tier 2 (C-01 onward) with groups, signals, draft wording, sources, confidence, guards, and new vs. extension; weak findings don't count; targeted searches where a combination lacks a source, otherwise recorded as a gap; nothing about minors; any source; no sub-agents. No changes to `index.html`, `inference-map.md` or tests.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `research-plan.md` §9: Pass C. Four targeted searches (broker income accuracy, nurses' night work, students and Macs, immigrants and phones bought abroad; the last found nothing); grid of 17 attributes × 8 groups; candidates C-01 to C-10 (new: C-03 pricing composite, C-05 night shift at the hospital, C-06 industry from network type; the rest extend T2-A/B/C/D/F/G/I); single-group and no-candidate lists; to-check list. Status line updated
- Not changed: `index.html`, `inference-map.md`, tests

**Verdict:** Fulfilled. ("yes")

---

## 024 · Research Pass D: advertiser view and risky combinations
**Date:** 2026-11-25

**Instruction:** Before moving on, another pass weighing research toward the advertiser/marketer perspective, and uncovering combinations of data or inferences that put someone at risk (fewer, but powerful).

**Interpretations:**
1. Pass D in §9. Part 1 (M-xx): how the ad industry would read a visitor from the page's signals (segments, categories, bid value, campaigns), leaning on industry sources (ad-platform audience categories, broker catalogs and marketplaces, ad-auction data and leaks, marketing case studies, trade press); accuracy not required. Part 2 (R-xx): combinations that expose someone to harm (re-identification, stalking or abuse, immigration, reproductive health, LGBTQ, scams, servicemembers), each with combination, who could exploit it, cases, how often it fires, draft wording; wording describes risk, not exploitation. Nothing about minors; weak findings don't count; no sub-agents; no changes to `index.html`, `inference-map.md` or tests.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `research-plan.md` §9: Pass D. Part 1: M-01 to M-07 plus composite M-10 "your ad profile" (M-08, M-09 unused; cross-references to Pass A). Part 2: R-01 to R-10 (R-04 reproductive health recorded as no person-level inference; sexuality, religion, politics recorded as impossible). Strongest list and to-check list. Status line updated
- Not changed: `index.html`, `inference-map.md`, tests

**Verdict:** Fulfilled. ("Yes")

---

## 025 · Source check
**Date:** 2026-11-25

**Instruction:** Check sources for all of the research first; the step after will be to update `inference-map.md`.

**Interpretations:**
1. For every finding in Passes A–D plus the to-check lists, compare the logged claim with the primary source; verdicts ✓ / ✎ (fixed in place) / ~ / ✗; summary added to §9; reliability changes flagged. Primary sources read via the web-fetch tool (a page reader, not a research sub-agent), which costs tokens. No changes to `inference-map.md`, `index.html` or tests. Asked: check "Candidate: none" items too?
   - *Answer:* skip them. *Confirmed* otherwise.

**Confirmed intent:** Interpretation 1, skipping "Candidate: none" items.

**Executed:**
- 12 page-reading batches plus direct checks: PDFs the reader couldn't parse (FTC 2014, Senate 2013, Berg et al., Hannak, Hupperich, Saxon & Feamster, W3C slides, Pew Trusts, Lotame, Appodeal, Eckersley) extracted locally and searched; OpenRTB 2.6 spec downloaded and read
- `research-plan.md` §9: corrections applied in place (A-01/02/03/05/07/08/09/11/14/32/36/37/39, B-01/02/03/05/10/11/22/23/26, M-01/02/05/06, R-02/03/04/07/09, Pass C nurses figure withdrawn); new "Source check" section with verdict table, corrections that change wording, and a still-unverified list. Status line updated
- Not changed: `inference-map.md`, `index.html`, tests

**Verdict:** Fulfilled. ("Yes")

---

## 026 · Inference map: research folded in
**Date:** 2026-11-25

**Instruction:** Update `inference-map.md` based on what the research found.

**Interpretations:**
1. Only `inference-map.md`, nothing built: update existing rules' sources, `[STAT]`s and confidence; add guards (teen iPhone, fiber, Android, location precision); add new rules and conclusions as Planned (C-01 to C-10, M-10, R-xx, targeting sentences, new network types); mark user calls with ⚑ Needs decision and unverified sources with ✗ source needed; update status, dropped list and data needs. Asked: every candidate or only the strongest? Targeting sentences inside each rule or in a separate part?
   - *Answers:* completionist, everything in one pass. Asked for examples of targeting statements; after examples, inside each rule.

**Confirmed intent:** Interpretation 1, everything, targeting statements inside each rule.

**Executed:**
- `inference-map.md`:
  - Status line, Standards (targeting/risk statements, minors) and Keys (✓ checked, ~, ✗ source needed, ⚑ Needs decision)
  - Part 1: battery row notes T1-37
  - Tier 1: sources, figures, guards and **Targeting** lines on T1-01/02/06/07/08/11/16/20/22/23/24/25/27/29/31/32/33/34; T1-14 removed; new Planned T1-35 Android, T1-36 Mac, T1-37 Low battery
  - Tier 2: research blocks on T2-A to T2-J; new Planned T2-K night shift at the hospital, T2-L pricing, T2-M ad profile, T2-N servicemember under strain, T2-O narrowed to a few people; R-04 recorded as a needs-decision note; research dropped list
  - Assembly notes 10–11; implementation note; Part 5 data needs
- Not changed: `index.html`, tests, `research-plan.md`

**Verdict:** Fulfilled. ("Yes")

---

## 027 · Review list in PRODUCT.md
**Date:** 2026-11-25

**Instruction:** Log in `PRODUCT.md` that the user will review the items that need a decision or sources; flag those, then move on.

**Interpretations:**
1. One `PRODUCT.md` item under "What we can learn" listing the ⚑ Needs decision, ✗ Source needed and US source needed items from `inference-map.md`; "flag" read as listing them there, since they're already marked in the map. Only `PRODUCT.md` changes.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `PRODUCT.md`: "Review before building" item with the three lists
- Not changed: `inference-map.md`, `index.html`, tests

**Verdict:** Fulfilled. ("Yes")

---

## 028 · Build pass: research into the engine
**Date:** 2026-11-25

**Instruction:** (After asking whether the engine had been updated: it hadn't.) Scope and do a build pass with what works with the page's current data, datasets in a separate step.

**Interpretations:**
1. Build into `index.html` all source-checked items that work with current data: new wording, figures, guards and targeting sentences on existing rules; new T1-36 Mac, T2-J (network path, no school districts), T2-L, T2-M, T2-O; remove T1-14; cap of two targeting sentences per rule. Leave out the review list, ZIP-income and GPC-list items, T1-35. Syntax check only; tests not edited or run; mark built in `inference-map.md`. Asked: optional sentences? Mac as an income signal (2009 figure)? T2-N narrowed or held?
   - *Answers:* put in the optional sentences; skip Mac as an income signal (figure too old); hold T2-N.

**Confirmed intent:** Interpretation 1 with optional sentences, no Mac income path, T2-N held.

**Executed:**
- `index.html` (engine only): T1-02 69% wording, advertiser sentence, teen guard; T1-06 "1 in 7"; T1-36 Mac; T1-31 state-level wording for unrecognized organizations, home-network risk sentence, ad-auction sentence; T1-32 Starlink rural, government, hospital, military sentences; T1-34 fraud flag; T1-14 removed; T1-16 impulse; T1-29 referrer age skew; T1-20 ad-blocker demographics; T1-22 NSA/Tails; T1-23 DNT; T1-27 (live) FTC and session-replay sentences; T2-A broker accuracy; T2-B segment names and payday ads; T2-C 93%, Facebook, scams; T2-G employer logs and sales software; T2-I travel pricing; new T2-J, T2-L, T2-M, T2-O
- `inference-map.md`: statuses updated; implementation note listing what's built and nine differences from the design
- Checks: engine and all inline scripts pass `node --check`. Tests not edited or run
- Not changed: tests, `research-plan.md`, `PRODUCT.md`

**Verdict:** Fulfilled. ("Yes")

---

## 029 · Research step complete; Next up updated
**Date:** 2026-11-25

**Instruction:** Consider the research step complete.

**Interpretations:**
1. `CLAUDE.md` "Next up": mark research ✓ (devlogs 020–028) and list next: test visitors, ZIP datasets and GPC list, the user's review list, IP reflection on the page; `research-plan.md` status line "Complete".
   - *Correction:* bundle the ZIP datasets and state GPC list next (before the test visitors). *Confirmed* otherwise.

**Confirmed intent:** Interpretation 1 with datasets first.

**Executed:**
- `CLAUDE.md`: step 3 marked done; new "Next, in order" list: (1) ZIP datasets + GPC list, (2) test visitors, (3) review list, (4) IP reflection on the page
- `research-plan.md`: status line "Complete (devlogs 020–028)"

**Verdict:** Fulfilled. ("Yes")

---

## 030 · ZIP datasets and state GPC list bundled
**Date:** 2026-11-26

**Instruction:** Pick up where we left off (Next up step 1: bundle the ZIP datasets and the state GPC list).

**Interpretations:**
1. Download public data into a new `data/` folder as compact local JSON, not wired into the engine: Census ACS by ZIP (income, rent, home ownership), IRS income by ZIP, the GPC state list; a README with sources; mark bundled in `inference-map.md` Part 5 and add a `PRODUCT.md` note. Asked: add language and industry to the ACS file? ACS and IRS, or ACS only?
   - *Answers:* add language and industry now; Census data is fine (skip IRS).
2. ACS 2020–2024 5-year by ZCTA in three files (income/housing; language at home, 13 groups; industry, 13 sectors); GPC states with law, date and source; `data/README.md`; notes in `inference-map.md` and `PRODUCT.md` (incl. M-03 resting on Census income). Not included: engine, tests, review list, industry wages. Census API without a key.
   - *Confirmed.*

**Confirmed intent:** Interpretation 2.

**Executed:**
- `data/zip-income-housing.json` (B19013, B25064, B25003), `data/zip-language.json` (C16001), `data/zip-industry.json` (C24050 lines 2–14): 33,772 ZCTAs. Deviation: the Census API now requires a key, so the bulk table-based Summary File was used instead (no key, no visitor data)
- `data/gpc-states.json`: 12 states (CA, CO, CT, TX, MT, NH, NE, NJ, MN, OR, DE, MD), four marked as explicitly naming GPC; Maryland and New Jersey dates flagged to confirm against statute
- `data/README.md`: sources, table IDs, download date, caveats (top-coding, missing values, margins of error, ZCTA ≠ ZIP)
- `inference-map.md` Part 5: ACS and GPC marked bundled, IRS marked skipped
- `PRODUCT.md`: "Bundled datasets" item with open checks
- Build script left in the session scratchpad (not in the repo)
- Not changed: `index.html`, tests

**Verdict:** Fulfilled. ("Yes")

---

## 031 · Finish the engine before tests: plan, and group E (map statuses)
**Date:** 2026-11-26

**Instruction:** Finish configuring the engine and the inference map before running tests; take care of all the open items first.

**Interpretations:**
1. Five groups: A (rules needing the new datasets), B (the user's review list), C (Planned items not needing new data), D (more datasets: release-year table, industry wages), E (tidy map statuses). Asked: which groups? How to combine device signals with area income? Data files don't load from `file://`: acceptable, or embed?
   - *Answers:* A, B, C and E. Be truthful: state the area's figures as what people there earn, pay and own, beside the device evidence, without a range for the person. Table `file://` until browser testing, but everything must work as normal while the engine is tested.
2. Order E → A → C → B, a check-back after each. The engine reads area data from one input object filled by the page; if the files don't load, the ZIP rules don't fire and everything else works. Note the `file://` question in `PRODUCT.md`. Syntax checks only; tests not edited or run.
   - *Confirmed.*

**Confirmed intent:** Interpretation 2.

**Executed (group E):**
- `inference-map.md`: statuses on every rule match `index.html`; 46 items marked Built; 14 marked Planned (ZIP data); T2-N Held; review-list items tagged; device age and T2-D timeframe marked as needing an unbundled release-year table; corrections (Mac not an income signal, M-07 payday figure and M-06 co-op clause not in built text, Sephora sentence California only); new status key; implementation note for devlog 031
- Not changed: `index.html`, tests

**Verdict:** Fulfilled. ("Yes")

---

## 032 · Group A: area data in the engine
**Date:** 2026-11-26

**Instruction:** (Group A of devlog 031's confirmed plan.) Build the rules that need the bundled datasets.

**Interpretations:** covered by devlog 031, interpretation 2 (income band stated as the area's figures, not a range for the person; engine reads area data from one input object; rules stay silent without it).

**Executed:**
- `index.html` engine: area helpers (`engArea`, household-weighted income percentile, tiers, money formatting); ZIP used only on home/small-ISP/satellite US connections within 50 km, reliable income only (≥200 households, MOE ≤30% or top-coded); T2-A high-income ZIP path, area sentence and low-income-ZIP suppression; M-03 and A-04 on T1-31 (two-sentence cap with R-07); B-01 on T1-32 (EarthLink added to the provider table); T1-23 state GPC sentence; T2-B low-income ZIP path; T2-C ZIP language support and A-05; T2-L location label; T2-M income-tier label
- `index.html` page: `Z` loaded from `data/` for US connections after the IP lookup, passed to the engine; silent on failure
- Fix during the build: top-coded ($250,000+) ZIPs have no margin of error and were being dropped; now accepted
- `inference-map.md`: statuses, implementation note for devlog 032; T2-B mobile-only path marked not buildable (carriers give no ZIP)
- `PRODUCT.md`: `file://` limit noted to revisit at browser testing; `data/README.md` updated
- Checks: `node --check` on inline scripts; throwaway engine run on five synthetic visitors in the scratchpad. Tests not edited or run
- Noted to the user: the Google Ads tier can appear twice (T1-31 and T2-M)

**Verdict:** Fulfilled. ("Yes")

---

## 033 · Group C: Planned items without new data
**Date:** 2026-11-26

**Instruction:** (Group C of devlog 031's confirmed plan.)

**Interpretations:** covered by devlog 031, interpretation 2.

**Executed:**
- `index.html` engine: T1-35 Android (only when T1-01 hasn't named the model); T1-37 low battery (observed fact only)
- Not built: T1-37's Uber sentence and T2-K (✗ source needed); T2-L behavior group (the profile is computed once while behavior accumulates; the FTC sentence is already live in T1-27); T2-N put back to the user
- `inference-map.md`: statuses and implementation note
- Checks: `node --check`; throwaway engine run in the scratchpad. Tests not edited or run

**Verdict:** Fulfilled. ("Yes")

---

## 034 · T2-N built; Uber sentence added
**Date:** 2026-11-26

**Instruction:** Reword T2-N so it says the visitor is a target for predatory organizations like lenders and for-profit colleges. Then: "Yes but add in the Uber surcharge piece despite not having a source."

**Interpretations:**
1. Build T2-N (military network + tight-budget signal) with: "…this makes you a target for predatory organizations, like lenders and for-profit colleges." Flagged that lenders are still unverified (only for-profit colleges is confirmed); asked whether to keep "lenders".
   - *Answer:* yes, as written, and also add T1-37's Uber sentence without a source.

**Confirmed intent:** Interpretation 1 plus the Uber sentence.

**Executed:**
- `index.html` engine: T2-N (phone only, matching "a phone and settings"); T1-37 Uber sentence
- `inference-map.md`: T2-N and T1-37 statuses; assembly rule 11 notes the Uber exception; implementation note
- `PRODUCT.md`: review list notes the Uber sentence shipped but is still worth sourcing
- Checks: `node --check`; throwaway engine run. Tests not edited or run
- Noted, not changed: institutional networks without a city read "in or near [state]"

**Verdict:** Fulfilled. ("Yes")

---

## 035 · Group B: the user's review list
**Date:** 2026-11-26

**Instruction:** (Group B of devlog 031's confirmed plan.) Source search results and decisions presented; the user answered: 1 build school networks in T2-J; 2 build T2-D's Spanish-dominance and ICE sentences, not the timeframe; 3 M-05 in T2-C and T2-M; 4 build the Parkinson's sentence; 5 reproductive health to Part Three later; 6 allow three targeting sentences on T2-B for Epsilon; 7 ship the lending study with "German" in the wording.

**Interpretations:**
1. As answered, plus: three targeting sentences also allowed on Spanish T2-C (M-05) and T1-27 (Parkinson's); the ad-click lending sentence left out (no ad-click detection); lobbying sentence reworded to the Washingtonian source (White House); Uber source upgraded to ~; T2-K stays out; A-04 figures and B-04 closed; map rules record the exceptions; `PRODUCT.md` review list updated.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `index.html` engine: T2-J school districts; T2-D B-20 and R-05 sentences; M-05 in T2-C and a T2-M label; T1-27 Parkinson's sentence after 50+ mouse movements; T2-B Epsilon; A-02 German wording on T1-02 (teen-guarded), T1-01/T1-35, T1-16; A-10 on government networks
- `inference-map.md`: statuses, assembly rules 10 and 11 exceptions, implementation note
- `PRODUCT.md`: review list marked resolved with what's still open
- Source search: Epsilon (DOJ via search, AARP), lobbying (Washingtonian 2019, read), Uber (TechRadar, Quartz, CBC); none found for T2-K or a US version of A-02
- Checks: `node --check`; throwaway engine run in the scratchpad. Tests not edited or run
- Raised: the Android lending sentence lacked the teen guard; "Washington, D.C.." doubled period (pre-existing)

**Verdict:** Fulfilled. ("otherwise yes this did what i intended")

---

## 036 · Android teen guard; D.C. period fix
**Date:** 2026-11-26

**Instruction:** Yes to both follow-ups from 035: add a teen guard to the Android lending sentence, and fix the doubled period.

**Interpretations:** the two follow-ups as offered in 035 (taken as confirmed by the user's answer).

**Executed:**
- `index.html` engine: teen-guard variables moved above T1-01; Android lending sentence (T1-01, T1-35) withheld on school networks and during school hours; T1-31 no longer adds a period after a place ending in one
- `inference-map.md`: note and status
- Checks: `node --check`; throwaway engine run

**Verdict:** Fulfilled. ("Your updates to teen guard and DC meet my intent.")

---

## 037 · Decision backlog cleared
**Date:** 2026-11-26

**Instruction:** Before test visitors, collect every pending decision so the user can answer them all. (Question answered with a 22-item list; the user then answered and asked for clarifications on 6, 9, 10 and 21.)

**Interpretations:**
1. Engine and map: Google Ads tier said once; "in [state]"; drop the ad-click sentence, T2-K and device age; narrow T2-E to low vision; accept T2-B grouping; drop price placeholders (keep tiers); check the Maryland and New Jersey GPC dates; fix the `PRODUCT.md` datasets line; all four cookies session-only, drop T1-26 and simGA's visit count and first-visit event. Defer 15–20 and 22 to a front-end pass. Asked: add a high-end GPU tier? Correct front-end text that would become false after the cookie change? Clarified 6 (area industry: data, truthfulness, reach), 9 (profile order), 10 (the [2] caps), 21 (serve vs. embed).
   - *Answers:* 6 option B with the user's wording ("The highest wages in this ZIP code are in the [x] industry. It's likely that advertisers will target you based on this information."); 9 front-end choice, later; 10 revisit after testing; 21 serve, flag for finalizing; add the GPU tier; correct the flagged front-end lines now.
2. Full set restated, with the T2-J area trigger (premium device or professional software on a home connection in a low-income ZIP) and the advertiser sentence recorded as an exception.
   - *Confirmed.*

**Confirmed intent:** Interpretation 2.

**Executed:**
- `data/zip-industry-earnings.json` (ACS B24031, 13 sectors, MOE >30% set to null); `data/gpc-states.json`: Maryland removed after reading the enacted act (§ 14-4607(F)(3) makes the signal one of two options), New Jersey 2025-07-15 confirmed
- `index.html` engine: T1-31 "in [state]" without a city, M-03 removed when T2-M shows the tier; T1-03 high-end GPU list feeding T2-A; T2-E narrowed; T2-J area path; T1-26 removed
- `index.html` page: session cookies (no expiry); simGA session count and first_visit removed; cookie cards and log line reworded; loader fetches the two industry files
- `inference-map.md`, `data/README.md`, `PRODUCT.md` (front-end pass section, cookies resolved, caps to revisit, serve-vs-embed before going live)
- Slip, corrected: build scripts were copied into `data/` and then removed (not in scope)
- Checks: `node --check`; throwaway engine run in the scratchpad. Tests not edited or run

**Verdict:** Fulfilled. ("Yes")

---

## 038 · Next up: moving on to testing
**Date:** 2026-11-26

**Instruction:** (After the user committed.) Update the Next up list to reflect that we're moving on to testing.

**Interpretations:**
1. `CLAUDE.md` → "Next up": datasets ✓ with the engine work (devlogs 030–037); review list ✓; test visitors as the current step, spelled out (p05/p06, IP and area data, new visitors, T1-26 removed); IP reflection moved into a front-end pass step; date updated. Nothing else changes.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `CLAUDE.md`: "Next, in order (set 2026-11-26)" rewritten as above

**Verdict:** Fulfilled. ("Yes")

## 039 · README for outside readers
**Date:** 2026-11-26

**Instruction:** Before moving on to testing, create a human-readable `README.md` in the project folder: an overview of the project, background on the workflow the user and Claude have been using, and a map of the files and folders.

**Interpretations:**
1. New root `README.md` for outside readers: project overview (what the page does, AI-free engine, privacy commitments, status, how to view), how we work (question/instruction loop, devlog, Learnings), and a file map. No other files changed.
   - *Correction:* reframe the purpose at the highest level: a web-based system that reflects to users the data routinely collected from them and why it matters, using an AI-free inference engine to correlate that data against freely available databases, to raise awareness of data privacy concerns.
2. Overview leads with what it is, how it works and why; commitments, status and viewing become a secondary layer. Other sections unchanged.
   - *Correction:* add an impactful thesis statement at the very top, interpreted from the user's intent (data privacy poorly understood; the scale of routine and third-party collection; "nothing to hide" fails for marginalized groups whose status can change overnight; immigrants and women seeking reproductive healthcare at risk; the project makes the collection and its dangers visible).
3. Drafted a thesis statement for review.
   - *Correction:* the user supplied the final wording.

**Confirmed intent:** Interpretation 2 plus the user's thesis statement at the top.

**Executed:**
- Created `README.md`: thesis (user's wording; "term" added to "is a often used", matching the user's earlier phrasing), project overview, how the engine works, privacy commitments, status, viewing instructions, how we work, file tree and reading guide. The author is unnamed ("its author").

**Verdict:** Fulfilled. ("Yes")

## 040 · README trims; milestone-only updates
**Date:** 2026-11-26

**Instruction:** Remove the "How the engine works" section from the README, retitle "How we work" to "How This Project Was Built", and don't update the README regularly: only at really big milestones, which the user will announce.

**Interpretations:**
1. `README.md`: delete the "How the engine works" subsection; rename "How we work" to "How This Project Was Built", text unchanged. Record the milestone-only rule as one line under "Project rules" in `CLAUDE.md` so later sessions know it.
   - *Confirmed.*

**Confirmed intent:** Interpretation 1.

**Executed:**
- `README.md`: "How the engine works" removed; "How we work" → "How This Project Was Built"
- `CLAUDE.md` → Project rules: added the milestone-only README rule

**Verdict:** Fulfilled. ("yes")
