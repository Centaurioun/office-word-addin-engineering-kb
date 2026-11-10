# ETKPlus — Scrum-quality Product Backlog refinement (design stage)

**Date: 2026-11-10. This is a proposed ordered backlog, not a sprint commitment.** [The Scrum Guide](https://scrumguides.org/scrum-guide) calls refinement an ongoing activity to further define Product Backlog items with description/order/size. It is **not** a mandatory separate Scrum event; this project's "Six-Cycle" is an internal critical review, not a formal Scrum artifact.

## Product goal

A reliable academic Word editing add-in that users can operate through **both native ribbon command groups/dropdowns and a focused, accessible right-hand taskpane**. Underlying functions remain one implementation per feature, and all document writes are previewed/authorized appropriately.

## Definition of Ready for a candidate development task

- User-visible outcome, constraints, scope and **explicit non-goals** written.
- Existing code location and source API/requirement-set identified.
- Acceptance criteria including native Word and negative paths.
- Dependencies (stable startup, installed manifest, actual build ID, Word fixture) recorded.
- Reversible implementation path, rollback and impacted data documented.
- Complexity sized by Codex developer after inspection; don't make up story points.
- Known blocker or environment limitation called out rather than hidden.

## Definition of Done for a shipped code change (not a universal definition of feature quality)

- Targeted failing test prior to fix when feasible; code review; build/typecheck/unit/QA results attached.
- Real Word feature check on exact build/fixture, including user approval, changed/unchanged scope and key negative case.
- No accidental manifest GUID/port or other add-in modification; old Home fallback preserved until replaced and tested.
- Accessible ribbon/pane labels; keyboard/focus behavior; layout at narrow Word widths for UI changes.
- Issue/PR evidence, version and rollback details recorded.
- If the host prevents a test, mark **BLOCKED** and leave item unfinished; do not treat missing tests as pass.
- Only merge/release with explicit project approval and release-specific quality gates.

## Ordered Product Backlog Items

| Order | Item / user value | Acceptance / testable output | Dependencies | Estimate |
|---|---|---|---|---|
| 0 | **PBI-00 Stabilize real Word startup** so editor can use any tool | Current build opens reproducibly; exact JS build identity; Issue #6 can proceed | Existing P0 PR #5 | TBD by developer |
| 1 | **PBI-01 Feature registry inventory** so no function gets lost during migration | All 60 HTML button elements classified; semantic feature IDs map to handlers, API sets, scope and SMK, zero unmapped actions | Code read access | TBD |
| 2 | **PBI-02 Ribbon/pane design contract** so users know where tasks live | One custom tab + dropdown IA, focused pane navigation, concise mock/spec and acceptance; user approves actual design before implementation | PBI-01 | TBD |
| 3 | **PBI-03 Isolated XML ribbon pilot** so primary commands are discoverable | Two groups, 3–5 entries and at least one working dropdown on Mac Word; Home fallback unchanged; no user document mutation on open | PBI-00/02 | TBD |
| 4 | **PBI-04 One semantic dispatch path** so both surfaces behave identically | Same feature from ribbon/menu and pane hits one handler; no duplicate mutation; unsupported API displays accurate state | PBI-03 | TBD |
| 5 | **PBI-05 Focused taskpane navigation** so user no longer scrolls through 60 controls | Compact route-based work area, persistent route/search, all existing features reachable via catalog, no regression to original capabilities | PBI-04 | TBD |
| 6 | **PBI-06 Gradual command-group migration** so high-use functions become fast | Cleanup/Academic/Profiles/Layout/Files/Navigation mapped group-by-group; each group native Word smoke verified | PBI-05 | TBD |
| 7 | **PBI-07 Academic revision evidence** for specialist users | Read-only citation/statistical audits first, then opt-in tracked-change/comments experiments, real Word field integrity checks | Stable baseline | TBD |
| 8 | **PBI-08 Delivery decision** for intended audiences | Document XML vs unified JSON, local vs enterprise/Marketplace, Mac/Windows requirement matrix, rollout/rollback | Product audience decision | TBD |
| 9 | **PBI-09 Full release verification** | 130 accepted scenarios, QA evidence, accessibility, document fidelity, Undo/Redo, build provenance | Production scope | TBD |

## Short acceptance stories

- **As an academic editor**, I can select an existing punctuation or profile tool from ribbon dropdown and see its preview in the right pane, so I do not have to hunt through long sections. **Given** text with known whitespace defects **when** I select the menu command **then** the right workflow opens without changing Word content; separate approval is still required for mutation.
- **As a reviewer**, I can click Academic Audit from ribbon and see categorized findings in the pane, with source/selection scope clearly marked and no write to Word.
- **As a returning user**, opening the existing Home > Paneli Aç command continues to work even before the new ribbon pilot is adopted.
- **As a Mac Word user**, keyboard and narrow window behavior are usable; overflow menus do not hide every command.
- **As a project maintainer**, a changed manifest can be reverted with old GUID/URL intact and no other sideloaded Office add-in touched.

## Process safeguards

Separate **product backlog** (Issue #7 and associated PBIs) from **incident triage** (PR #5 and Issue #6). Do not force product redesign into an unstable P0 worktree. Scrum-driven progress needs verifiable increments, not 130 test rows all marked blocked because a shared startup failure prevented testing. Avoid duplicate backlog entries and giant all-in-one Codex prompts. Evaluate PBIs one by one with a defined Word evidence contract and single Mac UI owner.

## Related

- [Six-cycle refinement](../guides/SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md)
- [Ribbon architecture](../guides/RIBBON-FIRST-WORD-ADDIN-ARCHITECTURE.md)
- [ETKPlus source-level inventory](../guides/ETKPLUS-CAPABILITIES-GAPS-ROADMAP.md)
- [Official OfficeDev Word dropdown example](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word)
