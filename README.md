# Data Transparency

> Data privacy is a term often used but poorly understood. A routine visit to an ordinary website hands over a tremendous amount of information about you, and that's before Google Analytics, Facebook, TikTok and LinkedIn add their own tracking. "I have nothing to hide" assumes two things: that the people reading your data mean you no harm, and that the line between safe and targeted will stay where it is. Neither is guaranteed. In today's political climate in the US and elsewhere, your status can change overnight. This project makes that collection visible: what a single page visit reveals about you, and how it could be used against you.

---

## What this project is

**Data Transparency is a web-based system that shows visitors the data routinely collected about them, and why it matters.**

When you open the page, it gathers the same kinds of information any ordinary website can collect: your device and its hardware, your browser and its settings, your language and time zone, your internet connection and approximate location, and how you behave on the page. It then reflects that information back to you in plain language.

It goes a step further. An **inference engine with no AI** correlates that routinely collected data against freely available public databases, such as US Census figures by ZIP code. From those combinations it draws the kinds of conclusions advertisers, data brokers and bad actors draw: your likely income, whether you rent or own, the industry you may work in, the language you may speak at home. Each statement is pre-written and triggered only by real data. Nothing is generated or guessed by a language model.

**The purpose is to raise awareness of data privacy and security concerns** by letting people see, about themselves, what is normally invisible.

### Privacy commitments
- **Nothing about a visitor is kept beyond a single visit.** Everything recorded stays in page memory only. It is never written to cookies, local storage or a server.
- **Outside services are vetted against the project's purpose.** Any third party that would receive visitor data is checked for logging, selling or sharing data, or being a registered data broker. For example, the IP lookup uses a service that states it keeps no logs, chosen over one that is a registered broker.
- **Public datasets are bundled with the page** (`data/`), so lookups against them happen locally.
- Google Analytics was removed. Its events are recorded locally and never sent.
- The page deliberately doesn't collect or reflect anything that singles out visitors under 18.

### Current status (November 2026)
- ✓ Data collection, including IP address and location
- ✓ Research phase and the inference engine built from it
- ✓ Census and state privacy-law datasets bundled
- **Next:** update and run the test visitors (`tests/`)
- **Then:** a front-end design pass

### Viewing the page
The page loads its datasets with `fetch`, which browsers block when a file is opened straight from disk. Serve the folder instead:

```sh
python3 -m http.server 3456
```

Then open <http://localhost:3456>.

---

## How This Project Was Built

This project is built collaboratively by its author and Claude (Anthropic's AI model, working in Claude Code). The collaboration follows a written working agreement, kept in `CLAUDE.md`, so that every change reflects the author's intent and nothing more.

**Every prompt is either a question or an instruction.**
- **Questions** are answered directly.
- **Instructions** (anything that changes the project) follow a five-step loop:
  1. **Reflect.** Claude restates what it believes the intention is, and changes nothing yet.
  2. **Iterate.** The author corrects that interpretation until it's right.
  3. **Execute.** Claude carries out the confirmed interpretation, and nothing beyond it.
  4. **Verify.** Claude asks whether the result fulfilled the intention.
  5. **Record.** The whole exchange is logged in `claude-devlog.md`: the request, each interpretation and correction, what changed, and the author's verdict.

**The system learns from its own record.** When a pattern shows up in the devlog, such as a recurring misreading or an approach that reliably lands, it's distilled into a short "Learnings" section in `CLAUDE.md`. Examples so far: scope changes to the narrowest reading, keep notes brief, and capture deferred items so they aren't lost.

**The work moves in deliberate stages:** collect data → reflect it back → infer from it → test. Research passes, each with a source check, sit between building the first engine and expanding it. Open questions and decisions for later are captured in `PRODUCT.md` as they come up.

---

## What's in this folder

```
data-transparency/
├── README.md              this overview
├── index.html             the site: data collection, inference engine and page
├── PRODUCT.md             project directions: ideas, decisions and open questions by feature
├── inference-map.md       design of the inference engine: standards, every rule, sources
├── data-audit.md          inventory of what a browser can reveal and what each piece suggests
├── research-plan.md       the research phase: method, passes A–D and all findings
├── claude-devlog.md       record of every instruction exchange between author and Claude
├── CLAUDE.md              the working agreement, next steps, project rules and learnings
├── data/                  public datasets bundled with the page
│   ├── README.md                    sources, fields and caveats for each dataset
│   ├── zip-income-housing.json      Census: income, rent and home ownership by ZIP
│   ├── zip-language.json            Census: languages spoken at home by ZIP
│   ├── zip-industry.json            Census: industry of residents' jobs by ZIP
│   ├── zip-industry-earnings.json   Census: median earnings by industry and ZIP
│   └── gpc-states.json              states whose law requires honoring Global Privacy Control
├── tests/                 made-up test visitors run through the engine outside the browser
│   ├── personas.json                20 test visitors and the rules each should trigger
│   ├── run-personas.js              runs them through the engine in index.html
│   └── results.md                   output of the latest run
└── .claude/
    └── launch.json        local preview server settings for Claude Code
```

### Where to start reading
- **To see it:** serve the folder and open `index.html` (see "Viewing the page").
- **To understand the thinking:** `PRODUCT.md`, then `inference-map.md` → "Standards".
- **To see the evidence behind the claims:** `research-plan.md` §9.
- **To see how it was built, step by step:** `claude-devlog.md`.
