# Data Transparency: Directions

Loose notes on where this could go.

**Built so far:** a page that reads visitor metadata and reflects it back. A rule-based inference engine (`inference-map.md`) turns the data into plain statements and, when enough independent signals line up, bolder conclusions. The page collects an extended set of passive and behavioral signals (`data-audit.md` §2–3). Google Analytics was removed; its events are recorded locally, never sent, for a future simulation. A test bed of 20 made-up visitors runs the engine outside the browser (`tests/`).

**Worth keeping:** the system is rule-based, with no AI. Every line is pre-written and triggered by real data.

---

## Areas of exploration

### What we can learn (the most interesting area)
- Find more data we can collect from visitors
- Find more inferences we can draw about the user from that data
- **Next steps (agreed 2026-11-24):** (1) IP address collection and reflection, which needs an outside lookup service, so settle the third-party trade-off and the footer's "processing is local" claim first; (2) feed IP data into the inference engine, including city-level location; (3) the deep research step below. Also in `CLAUDE.md` → "Next up."
- **IP address** (collected in devlog 015; recorder `N` in `index.html`, not displayed yet)
  - Lookup via **country.is**: open source, states it keeps no request logs, not a data broker. Location data comes from MaxMind GeoLite2 (MaxMind *is* a registered broker, but lookups run on country.is's server, so visitor IPs never reach MaxMind). ipapi.co was rejected: it logs queried IPs and is a registered data broker.
  - Returns IP, country, region, city, postal code, coordinates (with accuracy radius), time zone, network ID (ASN) and provider/organization name.
  - **When the site is hosted publicly:** switch to option (c), a server function at the host (e.g. a Cloudflare Worker reading `request.cf`), so no extra third party sees visitor IPs. country.is is for development.
  - **Not covered:** VPN, proxy, data-center and mobile-carrier detection. Free services don't offer it; paid ones are mostly data brokers. Possible substitute: an engine rule guessing from the provider name (AWS, known VPN companies, carriers).
  - Footer's "All browser-based processing on this page is local" line removed, since the IP lookup is no longer local.
  - **In the engine (devlog 017):** city/ZIP location, internet provider and type, VPN guess, clock vs. connection mismatch; now shown in the profile. Research step: extend the provider table, and consider ZIP-level Census data (e.g. median income) for bolder conclusions.
- **Inference engine** (built in `index.html`; design in `inference-map.md`)
  - Piecemeal rules (1–3 signals → one statement) plus threshold conclusions for bolder claims, with a light assembly step.
  - Reflections about the visitor only; say nothing where nothing can be inferred (except conspicuous absence from privacy tools).
  - **Research step (later):** an extensive pass to source every claim and fill the `[STAT]` placeholders (currently left out of the displayed text).
  - **Test visitors:** 20 made-up visitors in `tests/personas.json`; run `node tests/run-personas.js` → `tests/results.md`. p05/p06 need updating after the GA removal before the next run.
  - **Explication:** statements now say briefly what the information means where relevant (raised from about 1/10 to 3/10, devlog 013). Unavailable data is never mentioned (e.g. no referrer).
  - **Increase impact (circle back after the research step).** Two levers: (a) how insightful the inferences are, reviewed after research; (b) the language, adjusted later if needed. The user's examples of the target boldness:
    - "…Taken together, you are likely from a lower income bracket, which advertisers flag to target specific products and services to you."
    - "…Your phone's model number marks it as an international version, not one sold by US carriers. This suggests you've immigrated from Mexico to the US in the last 4 years and makes you a target for groups persecuting immigrants."
    - _Flag for research:_ "in the last 4 years" has no supporting signal yet, and the same signals also fit a visitor, a relative's phone or a device bought abroad. Research should support claims like this or reshape them.
  - **Guiding principles (devlog 018):** aim for "how did it know that?" about the person. Common knowledge (sunset) and area facts (ZIP statistics) are never statements on their own; area data only feeds conclusions about the person (income band, rent, home ownership, likely industry). Network type and employer are high-value. "In the last 4 years" could rest on the 2–4 year phone replacement cycle (to verify). Full detail in `inference-map.md`.
- **Minors (devlog 021):** the page doesn't collect or reflect anything that singles out visitors under 18. **To show visitors:** that this was an option and we chose not to take it, plus what data could be collected about minors and how it's used (Pass A examples in `research-plan.md`: Google estimating age from activity, A-18; 88% of teens owning iPhones, A-15; telling a child from an adult by taps alone, declined). Still allowed: stating a school-district network, and flagging a likely teen only to withhold income readings (A-15).
- **Review before building (devlog 027):** the user reviews these items in `inference-map.md` before any are built.
  - **⚑ Needs decision:** school networks in the industry conclusion (T2-J); all the immigration wording (T2-D); the marketers' Hispanic segments (M-05 under T2-C); the Parkinson's capability sentence (T1-27); reproductive health as explanatory content only (R-04)
  - **✗ Source needed:** lobbying audiences (A-10); the Uber battery finding (A-40, T1-37); Epsilon (R-06); Staples' figures (A-04); fiber income (B-04); the hospital night-shift base rate (T2-K)
  - **US source needed:** the lending-study sentences (A-02, on T1-02, T1-16, T1-29 and T1-35)
- **Cookies are memory, not sensing.** The page can only read its own cookies, not other sites'. Their value is memory across visits: visit history, first/last seen and earlier behavior. All of it can be shown back in real time, but only on the same browser.
  - **Revisit:** the page's own cookies keep data beyond a single session (persistent ID and visit counter 365 days, referrer 30 days), while the local recorders don't (see `CLAUDE.md` project rules). Decide whether the cookies should follow the same rule; the cookie demonstration in Part Two and the visit count in the profile depend on them.

### Showing it with impact
- Find impactful ways to present what we've learned back to the user
- **GA can't reflect individual data back.** What Google infers (location, demographics, cross-device links) stays on Google's side, and its real-time API only reports totals across visitors (and needs a server). GA's value here is showing what gets *sent*, not as a data source. _(GA has since been removed from the site; see the simulated tracking item.)_
- **Persistent sidebar of outgoing events:** shows each event as it would be sent to GA or other third parties. With GA removed, these are simulated events. _(circle back later)_
- **Simulated third-party tracking (pixels and GA)**
  - Show visitors what different tracking pixels (Meta, TikTok, LinkedIn, Google Ads…) collect and send.
  - **GA (removed from the site in devlog 011):** show what the site *would have* sent Google, built from the local recorder (`simGA` in `index.html`), which stores each event as a GA4 request with a simulated client ID. Simulate only what the site would attempt; whether a blocker would have stopped it isn't covered.
  - GA wording: clearly labeled as simulated, e.g. "A site running Google Analytics would have sent Google this when you arrived…", not "sent."
  - The simulated GA client ID resets on every reload. _(Decided in 012: that's fine.)_
  - **Fix later:** the Part Two Google Analytics panel now presents simulated data as real, like the Meta panel.
  - No real pixels connected to the site. Clearly labeled as a simulation.
  - Key point to convey: unlike GA, pixels tie the visit to the person's real account on that platform. _Open question: how best to show this._
  - _Open question:_ simulate with the visitor's real data ("what Meta would have received from you") or with generic examples?
  - Candidate for the outgoing-events sidebar.
  - **Fix later:** the current Meta Pixel panel in Part Two presents simulated data as real (a random "tracking ID" on each load, contradicting the footer). Rework it into the labeled simulation.
- **Revisit the canvas fingerprint** (from devlog 004):
  - The bar's high/low/blocked values (94 / 25 / 5) are placeholders I picked. Consider a published research figure, cited, as the baseline.
  - The incognito claim was removed from the log. It holds in Chrome but not in recent Safari, Firefox or Brave private windows, so if it comes back it should depend on the browser.
  - Consider hashing raw pixels instead of the PNG file, if we want to show the visitor their rendering or how much noise their browser adds.

### Dialogue depth
- Write more line options so the text reflects finer differences between hardware setups

### Tone
- The current language is too on the nose: it's stylized and alarmist enough to feel cartoonish
- Move toward neutral language and let the data itself do the alarming
- The purpose stays the same (making people aware of how much is collected and how it's used); only the voice changes
- Planned over the next couple of passes. _(The profile section was already rewritten to observed, neutral statements in devlogs 009–010; other sections are untouched.)_
- ~~**Fix later:** Part Two says "This page set four cookies," but GA adds at least two more (`_ga`, `_ga_7JHYWRBJ4Z`)~~ **Resolved (devlog 011):** GA removed, so the page now sets exactly four cookies.

### Structure & formatting
- Change the site's structure and formatting (details to come)
