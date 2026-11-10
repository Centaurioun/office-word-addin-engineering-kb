# ETKPlus × TransTools: capabilities, gaps, and next product increments

**2026-11-10 architecture audit (source-inspected, not live Word QA).** Under review: `mac-word-mvp/manifest.xml`, `public/taskpane.html`, source `src/core/*`, `src/office/*`, 45-feature [Issue #6](https://github.com/Centaurioun/ETKPlus-TransTools/issues/6). This is an **inventory and proposed roadmap**, not proof every declared button works.

## Revised prioritization after six-cycle review (2026-11-10)

**Product decision:** Keep both **ribbon dropdown/group entry points** and an enduring **right taskpane**, and keep **XML** for now. Do not remove or move existing tools before real Word smoke verification. A new Feature Registry and shared dispatcher are architectural seams only; they must not delay current P0 recovery. Source of truth: [Six-cycle product design](SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md), [Scrum backlog](../workflows/SCRUM-REFINED-PRODUCT-BACKLOG.md). Current code remains unchanged.

## Current implementation architecture

- A single XML manifest maps **Word Home > Word Araçlarım > Paneli Aç** to `ShowTaskpane`. It has **no dedicated ETKPlus custom tab** and no full ribbon command groups.
- Roughly 60 HTML `<button>` elements (including 2 common confirmation buttons) live on a single long 31 KB `taskpane.html`, with a section-anchor navigation bar.
- The code is already segmented into core rule engines (`src/core/text-rules.ts`, `academic-rules.ts`, `statistics-audit.ts`, `citation-review.ts`, `profiles.ts`, `document-hygiene.ts`, etc.) and Word adapters (`src/office/paragraph-actions.ts`, `review-actions.ts`, `table-format-tools.ts`, `image-tools.ts`, `file-workflow-tools.ts`, etc.).
- Existing functions cover: document navigation/search; selection; whitespace/punctuation/paragraph/tab cleanup; rule profiles; multi-find-replace; academic and citation/abbreviation/statistics audits; table format; inline image sizing/count/deletion; shape inventory; links and bookmarks; TXT/HTML/DOCX export; DOCX append; file inventory and split-plan **plans**, not actual multi-document merge/split.
- **Product risks/feature gaps are different from QA status**. Last 45-case smoke run couldn't load Word pane, so it produced 1 host startup failure and 44 BLOCKED. Do not label 44 features broken or 44 COMPLETE.

## User-facing gaps to tackle

| Gap | Evidence in current source | Proposed improvement | Acceptance indicator |
|---|---|---|---|
| Commands buried in vertical scroll | one continuous 31 KB HTML, many card sections and 60 buttons | Ribbon-first entry points and route-based pane | Primary features reachable in 1–2 interactions from Word ribbon |
| No recognizable specialized ribbon | manifest only has `TabHome` group, one Open Pane button | One `CustomTab` named ETKPlus, <=6 groups, optional Home/Review/Insert groups | Word Mac shows icons, menu labels, limited-width overflow works |
| Scattered action wiring | Many taskpane controls, domain-specific Office adapters | Stable semantic `FeatureDefinition` registry plus a shared `dispatch(id)` service | Ribbon and pane invoke exactly the same implementation/test |
| Too many duplicated preview/apply controls | Repeated `Tara / Düzelt`, `Onayla ve ...` pairs | Centralized preview/result/confirm pattern and focused tool-specific pane view | No silent document edits; same target/cancel semantics everywhere |
| Discoverability and scope | User must scroll; selection/body details are at top | Context badges ("Seçim" / "Belge" / "Tablo") per active feature, active selection context | User can see what will be inspected or changed before invoking |
| Feature completeness ambiguous | 45-item smoke checklist; many BLOCKED due to startup | UI-state-independent catalog with `SMK-`/QA mapping | Feature inventory shows **declared/implemented/verified/blocked** independently |
| Future distribution choices under-specified | XML add-in-only works, unified JSON documented but preview status varies | Separate ADR and prototype only when distribution targets known | Ribbon pilot works with XML; no premature migration |
| Academic editing not integrated with Word review | Rule-based auditing primarily read-only; no verified change tracking/comment workflow | Research Word tracked changes, comments, content controls as separate modules | Auditable non-destructive suggestions; no silent replacements |
| Citation import is heuristic | Basic author-year/citation checks; no full Zotero/CSL contract | Study Microsoft Word `.bib`/endnote sample and file-field interoperability, retain read-only review first | Exact citation identities maintained, no broken Word bibliography fields |
| User preferences/saved panel state | Current custom profiles may persist via local UI | Explicit per-user vs per-document storage contract | Reopening pane restores route; confidential text not stored in generic settings |
| Test/release discipline | P0 PR #5 and 130 test cases; build tokens .005/.007 | Keep stable feature registry with smoke and deep QA labels | One-pass function inventory then targeted regressions, without speculative fixes |

## Modular roadmap

### Phase 0 — Unblock and inventory (current Codex priority)
Get reliably loaded Word pane in actual Mac Word; run [SMK-01..45](https://github.com/Centaurioun/ETKPlus-TransTools/issues/6). Baseline gives feature-by-feature decision; avoid editing ribbon while host startup is broken. Use [isolated Microsoft Hello World](../troubleshooting/OFFICIAL-HELLO-WORLD-CANARY.md) as separate canary only when necessary.

### Phase 1 — Read-only UX/navigation proposal
Create a feature map `feature_id -> current HTML button ID -> existing action -> mode -> requirement set -> QA case -> proposed Ribbon group -> pane route`. Prioritize `Belge Haritası`, `Akademik Tara`, `Temizlik Merkezi`, `Profiller`, `Tablo Düzenle`, `DOCX Ekle` as candidate high-value entries. UI design audit for Mac narrow Word pane.

### Phase 2 — Minimum ribbon prototype (isolated feature branch)
Add one ETKPlus custom ribbon tab using **existing XML manifest** with 2 groups and 3–5 commands. Preserve Home Paneli Aç fallback. Use `ShowTaskpane` for safe navigation; explore different `SourceLocation` routes and taskpane IDs on actual Mac before claiming per-feature routing. No need to switch to JSON. Test separate clean Word profile/add-in manifest so current sideload isn't overwritten. Track install/host version/build and ribbon icon presentation.

### Phase 3 — Extract focused UI and common command dispatcher
Split long page into focused views (Cleanup, Academic, Layout, Files, Navigation), not dozens of separate instances. Introduce id-based dispatcher and feature capability guard; centralize human confirmation and stale-preview logic. Preserve current pure core and Word bridge, avoid rewriting tested features. Add state persistence by explicit storage boundaries.

### Phase 4 — Academic differentiation (research first)
- **Citation QA**: author-year, duplicate sources, abbreviations and journals; inspect [Microsoft BibTeX citation sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-citation-management) as a reference, NOT a Zotero replacement. Avoid formatting without CSL and target journal style.
- **Track changes and suggestions**: [Word tracked changes](https://learn.microsoft.com/en-us/office/dev/add-ins/word/manage-tracked-changes) can expose changes and metadata within documented scopes. Test Mac requirement set and privacy first. User can preview/rationalize before any insertion.
- **Comments, content controls and anchors**: [Completed Word tutorial](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/tutorials/word-tutorial) for stable anchoring; avoid overwriting reference manager fields.
- **Language templates**: Turkish/English author–year and statistics style profiles; add journal-specific policy separately; always record guideline version/date, and don't claim one rule fits all.
- **Word shortcuts**: [Custom keyboard shortcuts](https://learn.microsoft.com/en-us/office/dev/add-ins/design/keyboard-shortcuts) for two or three frequent read-only operations, conditional on SharedRuntime support.
- **Preference state**: [Persist add-in state](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/persisting-add-in-state-and-settings) explains Word `Document.settings` vs `localStorage`, partitioning, and document-bound settings. No thesis or patient content in accidental browser storage.

### Phase 5 — Delivery, packaging and release
- XML remains a production-supported manifest for Word; [Yeoman unified JSON option](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/yeoman-generator-overview) still says *preview* for Word, Excel, PPT; [unified manifest overview](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/unified-manifest-overview) confirms Mac 16.103+ but not universal support. The newest artifact format is not necessarily the optimal shipping choice.
- Investigate planned future target: local personal deployment, corporate centralized install, or Marketplace. Crucial: [Office.addin.showAsTaskpane()](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/show-hide-add-in) stopped working for **Marketplace** add-ins starting 2026-02-23. Do not hardcode a deep-link UX that only works for sideload/central deployment.
- Prepare TR/EN localization, permission/accessibility audit, icon and ribbon guidelines, privacy policy and packaging QA. Run complete acceptance suite only after stable smoke and major UX changes.

## Reference index for further deep dives

- [Office add-in commands — one custom core tab, dropdown controls](https://learn.microsoft.com/en-us/office/dev/add-ins/design/add-in-commands)
- [Office navigation patterns](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns)
- [Task panes design](https://learn.microsoft.com/en-us/office/dev/add-ins/design/task-pane-add-ins)
- [Office XML commands](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/create-addin-commands)
- [Official Word ribbon sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word)
- [Ribbon visibility and state updates](https://learn.microsoft.com/en-us/office/dev/add-ins/design/show-hide-controls-custom-tab)
- [Ribbon enable/disable controls](https://learn.microsoft.com/en-us/office/dev/add-ins/design/disable-add-in-commands)
- [Shared runtime](https://learn.microsoft.com/en-us/office/dev/add-ins/excel/configure-your-add-in-to-use-a-shared-runtime)
- [Track changes in Word](https://learn.microsoft.com/en-us/office/dev/add-ins/word/manage-tracked-changes)
- [Word add-in citations](https://learn.microsoft.com/en-us/office/dev/add-ins/word/citation-management)
- [Persistence / settings](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/persisting-add-in-state-and-settings)
- [OfficeDev Word sample atlas](OFFICIAL-WORD-SAMPLES-ATLAS.md)
- [Ribbon-first architecture proposal](RIBBON-FIRST-WORD-ADDIN-ARCHITECTURE.md)

**Do not mistake this product roadmap for an instruction to merge PR #5 or interrupt the current Codex startup debugging.**
