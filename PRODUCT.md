# Data Transparency: Directions

Loose notes on where this could go.

**Built so far:** a page that reads visitor metadata and matches it to prebuilt lines of text that appear depending on what the data shows. It also shows its own GA4 tracking live in a log panel.

**Worth keeping:** the system is rule-based, with no AI. Every line is pre-written and triggered by real data.

---

## Areas of exploration

### What we can learn (the most interesting area)
- Find more data we can collect from visitors
- Find more inferences we can draw about the user from that data
- **Inference engine** (draft in `inference-map.md`)
  - Piecemeal rules (1–3 signals → one statement) plus threshold conclusions for bolder claims, with a light assembly step.
  - Reflections about the visitor only; say nothing where nothing can be inferred (except conspicuous absence from privacy tools).
  - **Research step (later):** an extensive pass to source every claim and fill the `[STAT]` placeholders.
  - **Test visitors:** 20 made-up visitors in `tests/personas.json`; run `node tests/run-personas.js` → `tests/results.md`.
- **Cookies are memory, not sensing.** The page can only read its own cookies (including GA's `_ga`), not other sites'. Their value is memory across visits: visit history, first/last seen, earlier behavior, and when Google first saw this browser. All of it can be shown back in real time, but only on the same browser.

### Showing it with impact
- Find impactful ways to present what we've learned back to the user
- **GA can't reflect individual data back.** What Google infers (location, demographics, cross-device links) stays on Google's side, and its real-time API only reports totals across visitors (and needs a server). GA's value here is showing what gets *sent*, not as a data source.
- **Persistent sidebar of outgoing events:** shows each event as it's sent to GA or other third parties. _(circle back later)_
- **Simulated tracking pixels**
  - Show visitors what different tracking pixels (Meta, TikTok, LinkedIn, Google Ads…) collect and send.
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
- Planned over the next couple of passes
- **Fix later:** Part Two says "This page set four cookies," but GA adds at least two more (`_ga`, `_ga_7JHYWRBJ4Z`)

### Structure & formatting
- Change the site's structure and formatting (details to come)
