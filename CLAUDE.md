# Data Transparency: Working Agreement

## Workflow

Prompts are either **questions** or **instructions**.

**Questions:** answer directly. No check-back, no devlog entry.

**Instructions** (anything that changes the codebase or project files):
1. Reflect back what you believe the intention is. Make no changes yet.
2. Iterate on that interpretation until the user confirms it's correct.
3. Execute the confirmed interpretation, and nothing beyond it.
4. Ask whether the execution fulfilled the intention.
5. Record the whole exchange in `claude-devlog.md` (format at the top of that file), including the user's verdict once given.

## Next up (set 2026-11-24, end of first session)

Start here when resuming. First read `PRODUCT.md`, `inference-map.md` (incl. "Implementation notes") and the last few entries of `claude-devlog.md`. Then, in order:

1. **IP address collection and reflection.**
   - ✓ **Collected** (devlog 015): via country.is into recorder `N`, page memory only. Service choice, the later switch to a host server function, and the VPN-detection gap are in `PRODUCT.md` → "IP address". Footer's "processing is local" line removed.
   - **Still to do:** reflect it back on the page. Region and continent arrive as codes ("NY", "NA").
2. ✓ **Feed it into the inference engine** (devlogs 017–018): IP rules T1-31 to T1-34, new Tier 2 conclusions T2-G to T2-I; others marked Planned in `inference-map.md`.
3. ✓ **Deep research step** (devlogs 019–028): Passes A–D and a source check in `research-plan.md` §9; folded into `inference-map.md`; reliable, buildable items built into `index.html`.

**Next, in order (set 2026-11-26):**
1. ✓ **Bundle the ZIP datasets and the state GPC list** (devlog 030), then finish the engine (devlogs 031–037): area rules wired in, Planned items built, the user's review list and decision backlog cleared. Census ACS by ZIP and the GPC list in `data/` (see `data/README.md`).
2. ✓ **User's review list** (devlogs 035, 037).
3. ✓ **New test visitors, run** (devlogs 041–042): 30 new visitors (v01–v30) replace the old set; the runner supplies area data (`Z`). First run: every rule fired, all targets hit.
   - **Now: review `tests/results.md`.** Flagged so far: v07 (Deutsche Telekom isn't recognized as a provider, so T2-O treats a home connection as an employer); v19 (T2-J area path fired for an iPhone in a low-income ZIP; consistent with the rule, untargeted).
4. **Then: the front-end pass** (`PRODUCT.md` → "Front-end pass"), including reflecting IP data on the page (region and continent arrive as codes).

## Project rules

- **Local recorders keep nothing beyond a single session.** Data recorded about a visitor (`simGA`, `B`, `X` and any future recorder) stays in page memory only: never written to cookies, localStorage, IndexedDB or a server. The page's own demonstration cookies are a separate question, flagged in `PRODUCT.md` to revisit.
- **Check outside services against the project's purpose.** Before proposing any third party that would receive visitor data, check whether it logs, sells or shares that data, or is a registered data broker, and flag any conflict with the project's purpose. Cost and convenience come second (devlog 015).
- **`README.md` is updated only at major milestones, when the user says so.** Don't update it as part of routine work (devlog 040).

## Files

- `PRODUCT.md`: loose directions for the project, with ideas clustered into areas of exploration as they come up in conversation.
- `claude-devlog.md`: raw record of every instruction exchange.

## Learnings

Distilled from `claude-devlog.md`. Add or revise an entry only when a pattern emerges (recurring misinterpretations, approaches that reliably match intent), not after every exchange. Tell the user when this section changes. Keep each entry short and phrased as guidance.

- **Scope to the narrowest reading.** Corrections so far have removed things I added: displaying data, and verification steps (003). Propose the core change only, and offer extras like display, QA or follow-on edits as separate optional suggestions, not part of the interpretation. The user works in deliberate stages (collect → reflect → interpret → test).
- **Keep `PRODUCT.md` entries brief.** The user asked for a condensed synopsis instead of a full overview (005). Record conclusions and decisions in a line or two; detailed reasoning stays in conversation or the devlog.
- **Group by feature in `PRODUCT.md`.** Keep a feature's findings, constraints, open questions and fix-later notes together in one item, not split across sections by type (007).
- **Capture deferred items in `PRODUCT.md`.** When an exchange leaves something unresolved or explicitly for later, the user asks for a note so it isn't lost (004, 006, 012). Include that note in the interpretation by default.
- **Don't soften statements.** The user removed four hedges and disclaimers in one review (045): "this page can't know your own", "none of this means… charging you more", Uber's denial, the broker-inaccuracy line. Don't add disclaimers or hedges that soften a statement's impact; state the documented fact and stop. (Saying when something is a likelihood still applies.)
- **Don't run or edit tests unless asked.** Twice the user held off test runs while reviewing results (011, 013). Mention when a run would help; don't include it in the interpretation by default.
