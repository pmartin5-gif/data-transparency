# Data Transparency: Directions

Loose notes on where this could go.

**Built so far:** a page that reads visitor metadata and matches it to prebuilt lines of text that appear depending on what the data shows. It also shows its own GA4 tracking live in a log panel.

**Worth keeping:** the system is rule-based, with no AI. Every line is pre-written and triggered by real data.

---

## Areas of exploration

### What we can learn (the most interesting area)
- Find more data we can collect from visitors
- Find more inferences we can draw about the user from that data

### Showing it with impact
- Find impactful ways to present what we've learned back to the user
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

### Structure & formatting
- Change the site's structure and formatting (details to come)
