# Task routing for Word Office.js engineering

This file is bundled **inside the skill** so Codex can use it even when a copied skill is not co-located with the central Knowledge Base repository. Long-form background is available at the [central Office Word engineering KB](https://github.com/Centaurioun/office-word-addin-engineering-kb), if credentials/network permit.

## Decide the user's goal before touching code

| User goal | Work mode | First deliverable | Do not |
|---|---|---|---|
| "How should ribbon and taskpane work?" | **Product architecture** | Evidence-based alternatives, design/spec, PBI backlog | Start manifest changes before design approval or move all controls now |
| "Which tools work?" | **Feature inventory / smoke** | Complete matrix with build/fixture, COMPLETE/FAIL/BLOCKED/NOT_RUN | Turn a failed startup into 44 feature FAILs |
| "Add-in Error" | **Host debugging** | Same-host working/failing build diff and minimal canary/diagnostic | Change manifest/clear caches blindly |
| "Word Range lost punctuation" | **Scoped editing** | Repro selection/range edge test and minimal safe fix | Replace whole document to repair one space |
| "DOCX preview says stale" | **Snapshot safety** | Read-only paired snapshot diagnostics and final guard tests | Compare text only or bypass confirmation |
| "Add a Word command" | **Incremental feature** | Requirement-set/ribbon menu/host compatibility review; failing unit + native Word | Assume Excel-only example works in Word |
| "Verify ready for release" | **QA/release** | Exact build provenance + acceptance matrix + native Word evidence | Claim release gate from unit-only test results |
| "Improve this skill" | **Skill engineering** | Baseline pressure scenario, change summary, evaluator pass/fail | Equate YAML parse to agent discovery/behavior |

## Source confidence hierarchy

1. **API contract:** official Microsoft Learn Word API and requirement sets, current host/platform.
2. **Vendor implementation example:** OfficeDev repos and Microsoft docs source; version/license/host applicability checked.
3. **Native empirical result:** reproducible Mac Word observation tied to exact app JS build/Word host/fixture and screenshot or structured AX evidence.
4. **Upstream issue:** open/closed vendor issue status; hypothesis until local reproduction.
5. **Community anecdote:** Reddit, Stack Overflow, third-party repo; not an API promise.

Label claims `CONTRACT`, `SOURCE_INSPECTED`, `WORD_OBSERVED`, `HYPOTHESIS` or `BLOCKED`.

## Ribbon & taskpane rules

- Keep **both surfaces**. Use one custom core ribbon tab and optional groups on existing Word built-in tabs, plus one-level `Menu` dropdowns.
- Ribbon buttons/menus are entry points; taskpane is an enduring workbench for results, preview, options and confirmation.
- Use the existing XML add-in-only manifest unless an actual deployment requirement justifies testing unified JSON separately.
- Distinguish `ShowTaskpane`, `ExecuteFunction`, `Office.addin.showAsTaskpane()`, and shared runtime; inspect Marketplace limitations before choosing a route mechanism.
- Design a **single semantic feature dispatcher** mapped to existing `core/` pure logic and `office/` Word adapter implementation; no duplicate mutator for ribbon vs pane.
- Maintain existing entry point until new native Word pilot is verified. Start small: 2 groups, 3–5 entries, at least one dropdown.

## Document safety

- Snapshot preview is read-only; any mutation requires clear actual scope, explicit user confirmation and a final current-document/source guard in the same `Word.run` as insertion/edit. Lock duplicate submissions.
- Prefer `Range.search` and local range mutations; preserve styles, fields, tables, Zotero/BibTeX structures and Word Undo/Redo semantics. Do not conflate word-count preservation with style fidelity.
- If Office.js feature or change event unsupported, show truthful fallback/unsupported status; don't treat events as authoritative final guard.
- No real confidential manuscript, patient or unpublished data to AI services, local debug logs committed to Git, or public sample telemetry.
- One disposable Word QA document at a time; close with Don't Save. Never stop another add-in's server or mutate another add-in's installed manifest.

## Evidence and workflow

- Record `host, os/build, WordApi set, candidate build token, git SHA/dirty fingerprint, served JS identity, synthetic fixture checksum, actual Word observation, outcome`.
- For feature smoke, `COMPLETE` means *basic function demonstrated in real Word* only; not all security/deep acceptance passed. Keep `FAIL`, `BLOCKED`, `NOT_SUPPORTED`, `NOT_RUN` separate.
- For coding changes: inspect → failing test → minimal fix → unit/type/build → real Word → review → document exact SHA and limitation.
- For significant UX changes: brainstorm alternatives → user-chosen direction/spec → work plan → separate branch/pilot → native Word acceptance.
- If Superpowers skills are installed, use brainstorming, systematic-debugging, TDD, worktrees, code review and verification as appropriate. If no subagent support exists, do a sequential independent self-review and say so. Only one controller owns actual Word UI.

## Long reference routing (optional online)

- [Office platform & manifest decisions](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/guides/PLATFORM-AND-MANIFEST-DECISION-GUIDE.md)
- [Ribbon-first architecture](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/guides/RIBBON-FIRST-WORD-ADDIN-ARCHITECTURE.md)
- [Six-cycle product refinement](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/guides/SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md)
- [Scrum-refined backlog](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/workflows/SCRUM-REFINED-PRODUCT-BACKLOG.md)
- [Mac sideload and startup](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/troubleshooting/MAC-SIDELOAD-STARTUP.md)
- [FILE-07 OOXML nondeterminism](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/troubleshooting/FILE-07-OOXML-SNAPSHOTS.md)
- [QA evidence standard](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/workflows/QA-AND-EVIDENCE.md)
- [Official Word examples](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/guides/OFFICIAL-WORD-SAMPLES-ATLAS.md)
