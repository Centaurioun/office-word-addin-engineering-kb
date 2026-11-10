# ETKPlus × TransTools — six-cycle architecture refinement

**Decision analysis, 2026-11-10. Status: RECOMMENDED DESIGN, not implemented; actual Mac Word QA pending.** The six cycles below are a project-specific, structured iterative review method **not** a Scrum Guide ceremony or a Microsoft development standard. Sources: current ETKPlus XML manifest and taskpane; [Microsoft add-in commands](https://learn.microsoft.com/en-us/office/dev/add-ins/design/add-in-commands), [Office add-in UX best practices](https://learn.microsoft.com/en-us/office/dev/add-ins/concepts/add-in-development-best-practices), [navigation patterns](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns), [Word command dropdown sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word).

## Goals and confirmed user decisions

- **Both ribbon and taskpane are permanent, complementary surfaces.** Ribbon supports groups, **dropdown menus** and direct convenient entry points; pane supports previews, results, settings, long-form academic work and confirmation. No arbitrary removal of functionality.
- **Functional completeness and stability before mass migration.** Existing code is protected. A working operation remains reachable through its existing taskpane path until its ribbon equivalent is demonstrated.
- **XML manifest is acceptable** and already supports ribbon commands; change to unified JSON only when a documented deployment/integration benefit outweighs risk. Version and delivery choice are **separate** from UI design.
- One **custom ribbon core tab per add-in**, plus possible groups on Word built-in tabs; one-level dropdowns supported. Do not imply three independent custom tabs.
- Core actions should be UI-agnostic. No two versions of the same Word mutation in ribbon vs taskpane.

## Cycle 1 — Purpose and anti-goals

**Initial framing weakness:** A "ribbon-first" slogan could be misunderstood as replacing or shrinking the taskpane, or as a request to move everything now.

**Revised decision:** Call the target **dual-surface, command-centric architecture**. The ribbon is an access layer, the taskpane is the interactive workbench. Academic editors need fast repeatable commands and inspectable, explainable results. Retain taskpane-only capability where a ribbon item would be confusing or unsafe.

**Anti-goals:** migrating every button now; adding unrelated AI agents to production; rewriting all Word.run routines; automatically editing a manuscript because the user clicked a scan command; switching manifests just for visual freshness.

**Success measure:** core functions accessible without long scroll, with a single implementation and zero lost existing features.

## Cycle 2 — Current feature inventory, not imagined UI

**Observed:** existing manifest has one `TabHome` group and only `Paneli Aç`; approximately 60 HTML buttons (including controls) across navigation, cleanup, profiles, academic audits, table/image, links, exports and DOCX; existing `src/core` vs `src/office` modularity. [45-feature Issue #6](https://github.com/Centaurioun/ETKPlus-TransTools/issues/6) is a *basic smoke list*, not proof.

**Revised decision:** create a **Feature Registry** before UI restructuring:

`featureId,displayName,module,existingControlIds,existingHandlers,mutating?,scope,requirementSets,previewPolicy,confirmationPolicy,ribbonEntry,paneRoute,SMK_ID,implementationState,nativeWordEvidence`.

Categorize `declared`, `code-present`, `unit-verified`, `Word-verified`, `blocked`, `not-supported` separately; never compress into a fake COMPLETE.

**Acceptance:** 100% of existing buttons/actions mapped without alias collisions or orphan features. Do not equate one button with one full feature.

## Cycle 3 — UX alternatives and competition

| Approach | Strengths | Weaknesses | Decision |
|---|---|---|---|
| A: all 60 actions as ribbon buttons | Constant visibility | Massive crowding, hard to distinguish preview from apply; may overflow | **Reject** |
| B: only taskpane, no ribbon development | Minimum change | Persistent excessive vertical scrolling, poor discoverability | **Reject as target**, keep as fallback |
| C: ribbon dropdowns/groups + focused taskpane | Discoverability, scalable academic workflows, stable core, clear preview | Requires command routing and state coordination | **Recommend** |

**Prototype groups (not final navigation):** Temizlik, Akademik, Profiller, Tablo/Görsel, Belgeler, Gezinme. Each group contains one or two prominent buttons and a short one-level dropdown rather than dozens of repeated `Tara/Düzelt` pairs. Built-in Word tabs optional only after the private ETKPlus custom tab proves useful.

**Pane layout direction:** persistent compact header + current selection/document scope + search/tools navigation + **one active feature/workflow** + results/preview + explicit confirmation. Keep a global "All tools" directory for discoverability, not a forced removal of niche functions. Track task context when navigating between pane/ribbon.

## Cycle 4 — Architecture and API feasibility

**Risk discovered:** `ShowTaskpane`, `ExecuteFunction` and `Office.addin.showAsTaskpane()` are *not interchangeable*. A separate command runtime cannot silently share a JavaScript variable with an independent taskpane runtime. Shared runtime is a deliberate compatibility choice. Marketplace add-ins cannot rely only on `showAsTaskpane()` in current Microsoft guidance.

**Revised architecture:**
`Ribbon/Menu or Taskpane → stable Feature ID → capability gate & active-document scope → existing core/Office adapter → safe read-only result OR preview → human confirmation → final Word.run revalidation → mutation`.

Use an **initial small `ShowTaskpane` navigation pilot** with current XML before committing to dynamic panes/shared runtime. Preserve old Home "Paneli Aç" fallback. Avoid promising that tab clicks can synchronously deep-link an already-open pane until tested in Mac Word.

**Acceptance:** one semantic action registry, no duplicate mutators, no wrong-document edits, correct unsupported-feature feedback, graceful older Word behavior.

## Cycle 5 — Evidence, risk and test strategy

**Observed:** recent dev.007 sometimes loads but later smoke run was blocked by Add-in Error; 44 BLOCKED were not 44 broken features. FILE-07 succeeded under human approval on dev.005 but does not prove all styles, redo, save/reopen and double click.

**Revised plan:**
1. Stabilize native startup and real build identity; if blocked, isolate with official Word Hello World canary in distinct GUID/port.
2. One-pass SMK-01–45 **without** building ribbon yet. Keep result ledger build-specific.
3. Create isolated XML custom-tab pilot with **two groups and 3–5 entry points**; use read-only navigation/audit plus one preview-only action first.
4. Validate native Mac: ribbon visible, dropdown opens, Word pane opens correct feature, selection/body scope, text untouched on preview/cancel, keyboard, resize/overflow and existing Home fallback.
5. Expand one stable group at a time; 130-case full acceptance is a later release gate, not prerequisite to writing a design.

**Non-negotiable:** No production document tests; no silent mutation; no arbitrary weakening of stale preview protection; no false 'Word PASS' inferred from JS mocks or external samples.

## Cycle 6 — Simplification and Scrum-quality backlog refinement

**Discard unnecessary immediate work:** full JSON migration, second/third custom tab, new frontend framework, wholesale component rewrite, hundreds of ribbon actions, direct AI document-editing agent, auto-opening every document.

**Keep and phase:** inventory → design/spec approval → tiny isolated ribbon spike → shared dispatch and focused pane routes → gradual conversion → academic enhancement → distribution decision.

Use [2020 Scrum Guide](https://scrumguides.org/scrum-guide): Product Backlog is ordered and emergent; refinement continuously breaks items into smaller, clearer work. Six cycles are an internal review technique, not an official Scrum event. [Scrum-refined issue checklist](../workflows/SCRUM-REFINED-PRODUCT-BACKLOG.md) provides deliverables, owner decisions, Definition of Ready and Definition of Done. Avoid overcommitting to sprint durations or inaccurate estimates.

## Final revised architecture principle

**Build behavior first, expose it through both surfaces second.** Improve contextual usability without blocking the current feature set. Ribbon dropdowns can surface families of existing functions; the right pane stays the home for academic evidence and multi-step editing.

### Decisions needing real product evidence (not guesses)

- Whether users prefer an ETKPlus-only custom tab or one shortcut group in Word Home in addition.
- How many ribbon commands fit Mac Word at common window widths and locale.
- Whether feature-to-pane deep-link routing survives taskpane reopen/reload and multiple documents.
- Exact shared runtime/Marketplace compatibility and deployment needs.
- Which advanced academic functions deserve independent one-click commands versus pane-only.
