# Ribbon-first Word Add-in architecture — ETKPlus design reference

**Curated 2026-11-10.** Evidence: actual ETKPlus XML manifest/HTML/TypeScript files on `fix/mac-word-p0-blockers-20261109`; Microsoft Learn Office UI, ribbon commands, navigation, runtimes, manifest, keyboard shortcuts, Word samples. **PROPOSAL / NOT IMPLEMENTED IN WORD.** This page is a reusable product-design/engineering playbook, not a claim that the described functions have passed QA.

## Revision: dual-surface, not immediate ribbon migration (six-cycle review, 2026-11-10)

The user explicitly selected **both ribbon and right-hand taskpane** and asked not to move/remove existing controls until functionality is stable. Treat "Ribbon-first" as the *long-term navigation priority*, **not** as an immediate UI rewrite. Support dropdown `Control xsi:type="Menu"` using Microsoft's Word XML sample, with at most one custom core tab and optional groups on built-in tabs. Preserve every existing feature via an all-tools catalog in the pane. See the [six-cycle reviewed contract](SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md) and [Scrum-refined backlog](../workflows/SCRUM-REFINED-PRODUCT-BACKLOG.md). This addendum supersedes any wording below that implies current pane replacement. No code has been changed or piloted in native Word for this architecture review.

## Product decision

**Ribbon is the main command surface; task pane is the focused workflow/results surface.** Preserve the existing Office.js document-editing engines, then introduce a shared semantic command registry so that ribbon and taskpane are two adapters to the **same** command. Avoid 60 independent ribbon buttons and duplicate mutation code.

### Verified current ETKPlus baseline

- `mac-word-mvp/manifest.xml`: XML add-in-only `OfficeApp`, Word `Document` host, WordApi 1.1 minimum, `https://localhost:3001/taskpane.html`, **one Word Home tab group and one `ShowTaskpane` button** (`WordTools.Open`).
- `mac-word-mvp/public/taskpane.html`: approximately 30.9 KB and **60 button elements including dialog Cancel/Confirm**, with many nested sections for Word navigation, 7 cleanup rules, profiles, find-replace, scholarly audits, table/image tools, links/bookmarks, export, DOCX append, splitting/batch planning.
- `src/core/` and `src/office/` are already separated by capability; do **not** reimplement Word actions from scratch simply to add ribbon UI.
- P0/QA state is independent: Word Add-in Error blocked a later 45-feature smoke run; historical `.005` user-approved DOCX append and `.007` prior load observations exist. New ribbon UI work is **not** an alternative to establishing a reproducible startup baseline.

## Actual Microsoft ribbon constraints

1. **One custom core ribbon tab per Office Add-in**, not two or three. See [Microsoft add-in commands](https://learn.microsoft.com/en-us/office/dev/add-ins/design/add-in-commands) and [CustomTab reference](https://learn.microsoft.com/en-us/javascript/api/manifest/customtab?view=word-js-preview).
2. The same add-in can put **custom command groups on built-in Office tabs** (e.g. Home, Insert, Review) as well as its own custom core tab, subject to manifest support and the tested Word host. This can give a spread across several visible tabs **without** creating multiple custom tabs.
3. Within tabs use **buttons and one-level dropdown menus**. No nested submenus; Word may collapse groups into overflow on narrower windows.
4. Microsoft recommends approximately **6 groups per tab, <=7 commands/group**, no unnecessary clutter and human-readable action labels. These are design guidelines, not schema hard limits. Icon/label sizes vary with window width.
5. Custom contextual tab behavior is platform/requirement-set specific; shared-runtime guidance calls dynamic contextual tabs **Excel-only** in some configurations. Do not promise Word contextual tabs without exact Mac Word/API validation. Instead start with regular groups and stateful enabled/disabled controls.
6. [Custom tab placement](https://learn.microsoft.com/en-us/office/dev/add-ins/design/custom-tab-placement) details apply only to **PowerPoint on the web** according to current Microsoft documentation; don't promise ETKPlus a guaranteed spot before/after Home on Word Mac.

## Proposed user-facing ribbon map

| Visible surface | Group / menu | Command entry points | Behaves by default |
|---|---|---|---|
| **ETKPlus** (one new custom tab) | **Temizlik** | Temizlik Merkezi; Fazla Boşluklar; Noktalama; Sekmeler | Read-only scan or open exact cleanup preview; **never auto-mutate just by opening** |
| ETKPlus | **Akademik** | Akademik Denetim; Atıflar/Kısaltmalar; İstatistik; Tablo/Şekil | Read-only audit, report results in focused taskpane |
| ETKPlus | **Profiller** | Akademik TR; Academic EN; OCR; Özel Profil | Open profile-focused pane with rule selectors and human confirmation |
| ETKPlus | **Tablo & Görsel** | Tablo Düzenle; Tabloya Sığdır; Görsel Boyutu; Şekil Envanteri | View selected-object overview; changes via preview + explicit confirm |
| ETKPlus | **Belgeler** | DOCX Ekle; Dışa Aktar; Bölme Planı; Toplu Envanter | Open exact workflow; no silent file import or user-content export |
| ETKPlus | **Gezinme** | Belge Haritası; Gelişmiş Ara; Başlığa Git; Yer İşaretleri | Read-only selection/find/navigation |
| **Home** (existing tab, optional) | ETKPlus Hızlı Araçlar | Temizle ve Akademik Tara shortcuts | At most 1–2 high-frequency entry points, avoid duplicated menu carpet |
| **Review** (existing tab, optional) | Akademik Kontrol | Atıf Denetimi, İstatistik Kontrolü | Non-mutating review workflow |
| **Insert** (existing tab, optional) | Belge ve Tablo | DOCX Ekle, Tablo İşlemleri | Opens focused preview, **not** an immediate mutation |

Do not implement all rows at once. Start with **one custom tab and 2 small groups / 3–5 entry points**, test actual Word Mac before extending, then distribute into built-in tabs if user testing finds value.

### Minimal conceptual layout

```text
Word ribbon
  Home   Insert   Review   [ ETKPlus ]   View
                            |
       +----------+---------+---------+-----------+---------+---------+
       | Cleanup  | Academic| Profiles| Table/Img | Files   | Navigate|
       +----------+---------+---------+-----------+---------+---------+
             |            a semantic command / deep link / focused pane
             v
    Task pane: compact header + selected workflow + preview/results + confirm
             |
             v
    One action registry -> existing core/office feature engine -> Word.run
```

## Command design: prevent two implementations of the same tool

Propose a central `FeatureDefinition` or `CommandDefinition` map, imported by both ribbon commands and taskpane:

```ts
type FeatureDefinition = {
  id: string;              // e.g. "cleanup.punctuation", stable across UI changes
  title: string;
  group: "cleanup"|"academic"|"profiles"|"table"|"images"|"files"|"navigation";
  scope: "selection"|"body"|"document"|"active-table";
  mode: "read-only"|"requires-preview-confirm";
  route: string;           // focused pane route, e.g. "cleanup/punctuation"
  requirementSets: Array<{name:string;version:string}>;
  smokeId?: string;        // SMK-xx traceability
  availability: "supported"|"fallback"|"not-supported";
};
```

This is illustrative schema design, **not a ready-to-paste implementation**. Actions and Word handles should stay in `src/office/*`; pure logic in `src/core/*`. Extract UI components as feature routes; share a single preflight/confirmation/error/busy-state service. Generate **stable** manifest resource IDs from verified mapping only if practical; do not rewrite XML blindly.

**Single command path:** `ribbon click` or `taskpane click` -> `dispatch(featureId)` -> `capability guard` -> `document/selection scope` -> `read-only audit OR preview` -> `explicit approval` -> `fresh apply-time guard + Word.run action` -> `result and QA evidence`. Do not expose two mutating implementations; do not auto-run destructive functions from ribbon command execution.

## Ribbon command types and distribution

- **`ShowTaskpane`** (XML) or `openPage` (unified JSON) opens a pane and is the low-risk first step. A manifest may define separate resource URLs for taskpane-focused entry points; prototype actual Word behavior, pane identity and state before adopting multiple URLs.
- **`ExecuteFunction`** runs JavaScript without a taskpane. Suitable for *safe short read-only navigation/audit* if the result can be surfaced. For destructive actions prefer opening a preview workflow; simple function commands have a **5-minute execution limit** and may use a separate runtime.
- **Shared runtime** can coordinate command state and taskpane, and enable programmatic pane opening; it requires explicit manifest/runtime setup and supported requirement sets. It is a **separate architecture step** needing Mac Word testing, not implied by merely having `<FunctionFile>`.
- **Important 2026 distribution caveat:** [Office.addin.showAsTaskpane() and hide()](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/show-hide-add-in) only work for **centrally deployed or sideloaded** add-ins as of **2026-02-23**; Marketplace add-ins ignore `showAsTaskpane`. Accordingly, any ribbon deep-link design depending solely on `Office.addin.showAsTaskpane()` is **NOT Marketplace-safe**. Use first-class `ShowTaskpane`/openPage actions with tested route handling for a Marketplace-oriented design, or explicit fallback. See [Office navigation patterns](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns).
- Keyboard shortcuts require `SharedRuntime 1.1` and platform-specific support. Word Mac >=16.88 documented; check current host before assigning shortcuts. Avoid clashes with Word's own keyboard behavior. See [custom shortcuts](https://learn.microsoft.com/en-us/office/dev/add-ins/design/keyboard-shortcuts).
- Dynamic enabled/visibility controls use `Office.ribbon.requestUpdate` and requirement sets, and may require a host restart after manifest changes. These augment UX but **do not replace safety checks**. See [disable commands](https://learn.microsoft.com/en-us/office/dev/add-ins/design/disable-add-in-commands) and [show/hide custom tab controls](https://learn.microsoft.com/en-us/office/dev/add-ins/design/show-hide-controls-custom-tab).
- Office Dialog API can provide small input/progress workflows, but Microsoft's design guidance prefers document interaction in the taskpane. An Office dialog is a **separate runtime** and is not interchangeable with unsafe `window.confirm/prompt/open`. See [Office dialog](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/dialog-api-in-office-add-ins).

## Why a long right-side scroll is not sustainable

Current `taskpane.html` uses a single continuous page of sections. Proposed next UI is a focused SPA with an always-visible compact navigation (e.g. Cleanup / Academic / Layout / Files / Navigate), optional search and short result lists. Ribbon shortcuts open the appropriate module. Use nested accordions only for **secondary** detail, not as the primary navigation system. Preserve user context when possible; if storing document settings vs localStorage, observe confidentiality/document identity boundaries. See [Microsoft navigation patterns](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns).

## Suggested delivery sequence — separate from current P0 PR

**Gate 0 — Functional baseline:** Recover reproducible Word startup; finish one-pass SMK-01–45 inventory. Don't declare every feature complete because it renders a button.

**Phase 1 — Architecture-only design:** document existing 60 button IDs and map them to stable semantic feature IDs, current office implementation, read/write mode, scope, API set, preview/confirmation, expected results, SMK case, and proposed ribbon group. Prepare a UX flow and schema; no user document mutations.

**Phase 2 — Ribbon pilot in separate branch:** implement XML `CustomTab` **ETKPlus** with 2 groups and 3–5 high-usage commands, preferably read-only or safe `ShowTaskpane`. Keep existing Home "Paneli Aç" as fallback. Validate XML manifest schema, GUID preservation, icon resources, Mac sideload, panel open, shortcut route, keyboard, narrow Word window and fallback. Use a clean separate QA sideload configuration if simultaneous testing might affect current build.

**Phase 3 — One-command core and focused panel:** gradual mapping from existing 60 buttons, unified dispatch, per-feature views; no duplicate Office.js mutation. Test state consistency, Cancel/Confirm, scope, save/reopen for relevant functions.

**Phase 4 — Academic product differentiation:** compare Word citation management `.bib`/endnote sample, Word tutorial, Zotero interoperability and scientific statistics/citation rules. Avoid claiming certainty from heuristics, respect user privacy and actual Word field structure.

**Phase 5 — Packaging/distribution decision:** evaluate XML (production supported) vs unified JSON (newer ecosystem, Word/PPT/Excel generator still marked preview) on a **separate prototype branch**. Do not migrate solely for ribbon access; XML already supports ribbons. Evaluate Marketplace vs central deployment **before** depending on shared-runtime pane deep links.

**Phase 6 — Full QA and release:** after feature smoke + ribbon functional tests pass, return to 130 acceptance cases, compatibility matrix, accessibility, undo/redo and document integrity. PR #5 remains independently gated until reviewed.

## Sources (official)

- [Ribbon command concepts, one custom core tab, groups and menus](https://learn.microsoft.com/en-us/office/dev/add-ins/design/add-in-commands)
- [CustomTab XML reference](https://learn.microsoft.com/en-us/javascript/api/manifest/customtab?view=word-js-preview)
- [Office UI elements](https://learn.microsoft.com/en-us/office/dev/add-ins/design/interface-elements)
- [Navigation patterns (ribbon vs taskpane)](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns)
- [Create commands with XML add-in-only manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/create-addin-commands)
- [OfficeDev Word commands example](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word)
- [Word command keyboard shortcuts](https://learn.microsoft.com/en-us/office/dev/add-ins/design/keyboard-shortcuts)
- [Shared runtime configuration](https://learn.microsoft.com/en-us/office/dev/add-ins/excel/configure-your-add-in-to-use-a-shared-runtime)
- [Programmatic pane visibility and Marketplace limitation](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/show-hide-add-in)
- [Office dialog guidance](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/dialog-api-in-office-add-ins)
- [Microsoft unified manifest overview](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/unified-manifest-overview)
- [Microsoft Yeoman generator's unified manifest preview warning](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/yeoman-generator-overview)

**Update policy:** treat Mac host and release status as time-sensitive. Record any verified exceptions to this recommendation as a new decision record; don't silently rewrite this baseline.
