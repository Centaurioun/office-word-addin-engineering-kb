# Source and link verification log

**Checked:** 2026-11-10 (UTC), repository-content review. Links are references, not copied source. A reachable URL validates link availability only; it does not validate every technical statement on that page.

## Microsoft Learn and Microsoft samples

Every Microsoft Learn and Learn sample URL listed in `SOURCE-CATALOG.md` and the troubleshooting notes returned HTTP 200 to a HEAD request. The Microsoft Learn MCP endpoint was used for a real query (`Word JavaScript API requirement sets getOoxml insertFileFromBase64`) and returned relevant Word API and requirement-set documentation. The MCP connection is operational; this does not validate unrelated queries or configurations.

The Safari Inspector, Mac runtime logging, Word JavaScript API, requirement sets, OOXML guide, import samples, Copilot prompt library, and Learn MCP reference were checked as linked pages. Check current API set and platform applicability again before implementation.

## OfficeDev GitHub and issue tracker

Official repositories and documentation links listed in `SOURCE-CATALOG.md` returned HTTP 200, except the previous development-kit URL, which was a wrong repository slug (404). It is corrected to [OfficeDev/OfficeAddinDevKit](https://github.com/OfficeDev/OfficeAddinDevKit), which search confirmed is the public project.

The following issue states were queried through the authenticated GitHub API on 2026-11-10. These are report statuses, not proof of behavior on every Word build:

| Issue | GitHub state / evidence | Interpretation limit |
|---|---|---|
| [#6973](https://github.com/OfficeDev/office-js/issues/6973) | Open; updated 2026-10-13 | Mac/Windows sideload persistence report; local cause unproven. |
| [#6597](https://github.com/OfficeDev/office-js/issues/6597) | Open; updated 2026-08-28 | Work-account / add-ins store loading report; account-scoped. |
| [#4078](https://github.com/OfficeDev/office-js/issues/4078) | Closed completed; Microsoft comment 2026-07-19 says fix rolled out, closed 2026-07-20 | Historical build 16.81 `w14:paraId` issue; not evidence about `w:rsidR`. |
| [#6703](https://github.com/OfficeDev/office-js/issues/6703) | Closed completed; maintainer comment 2026-06-02 says offending change was turned off | Regression report for Word 16.109 list indentation; the issue comment is not an independent test across hosts. |
| [#2225](https://github.com/OfficeDev/office-js/issues/2225) | Closed, not planned | Older Word on web `Range.expandTo` Undo-history report. |
| [#1827](https://github.com/OfficeDev/office-js/issues/1827) | Closed completed; Microsoft response 2021-05-19 said there were no plans for an UndoRecord capability | Historical response only; not a current roadmap statement or an API contract. |
| [#6952](https://github.com/OfficeDev/office-js/issues/6952) | Closed completed; updated 2026-09-23 | Store add-in failure cluster; not the same as Mac developer sideload persistence. |

## npm and community sources

The npm package pages returned HTTP 403 to automated page requests, so package existence, latest dist-tag, license metadata, and deprecation metadata were checked through the official npm registry JSON API instead. On the check date, the linked `office-addin-manifest`, `office-addin-debugging`, `office-addin-mock`, `office-addin-test-helpers`, and `office-addin-test-server` packages resolved to releases 3.0.1, 7.0.1, 4.0.1, 3.0.1, and 3.0.1 respectively; each registry record reported MIT and no deprecation notice. Versions change; check package docs before use.

The Stack Overflow page returned HTTP 403 to the direct automated request, while search indexing found the matching Undo-history discussion. The linked Reddit pages also returned HTTP 403, so their contents were not independently re-read in this pass; retain them as low-confidence anecdotal clues only. The YouTube link resolved, but its architectural claims were not treated as authoritative.

The example URL `https://example.org/a.b` in a test fixture description is illustrative text, not a source link. The `https://localhost:3001/taskpane.html` runtime URL could not be checked because the local TLS certificate was not trusted by the HTTP client; this is expected to require the project’s local certificate and is not a public resource.

## Private source-project links

The ETKPlus case guide's private project, PR, and QA artifact links were checked with the authenticated GitHub account. The exact referenced artifact paths exist at their pinned commits. They remain subject to private-repository access and are external case evidence; no QA artifacts or fixtures were copied here. PR #5 remains open and draft.

## Additional platform/manifest and official Word sample review — 2026-11-10

**Explicit user-provided starting URLs (all 6 inspected):**

1. [Office Add-ins platform](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/office-add-ins) — manifest+hosted web app separation, basic Office.js capability.
2. [Microsoft 365 app package](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/app-package-for-microsoft-365) — JSON unified manifest and icon ZIP distribution, **not** the current XML manifest's package requirement.
3. [Unified app manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/unified-manifest-overview) — Mac Word support from version 16.103 as currently documented, coverage still varies by host/platform.
4. [OfficeDev sample repository](https://github.com/OfficeDev/Office-Add-in-samples) — inspected recursive sample directory tree and Word-related sample READMEs.
5. [Word Hello World](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world/word-hello-world) — inspected README, XML localhost manifest, taskpane HTML and unified sample JSON; the HTML contains optional external telemetry image request that should not be copied to a privacy-sensitive project.
6. [Hello World family](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world) — inspected overview README and host subdirectories.

**Linked Microsoft Learn reference pages inspected and documented:** [add-in-only XML manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/xml-manifest-overview), [compare XML and JSON](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/json-manifest-overview), [manage both variants](https://learn.microsoft.com/en-us/office/dev/add-ins/concepts/duplicate-legacy-metaos-add-ins), [Mac XML sideload](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac), [Office.onReady initialization](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/initialize-add-in), [Office requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/office-versions-and-requirement-sets), [runtime requirement checks](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/specify-api-requirements-runtime), [Word tutorial](https://learn.microsoft.com/en-us/office/dev/add-ins/tutorials/word-tutorial), [Word events](https://learn.microsoft.com/en-us/office/dev/add-ins/word/word-add-ins-events), [Office Add-in commands](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/create-addin-commands), [Mac Safari Inspector](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac), [runtime logging](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/runtime-logging), and [citation management](https://learn.microsoft.com/en-us/office/dev/add-ins/word/citation-management). This is a **selected relevant-link traversal**, not a claim that every external link on all Microsoft pages has been exhaustively opened.

**Additional official sample READMEs inspected:** Word command buttons; auto-open document taskpane; completed Word tutorial; import template; BibTeX citation management; get/edit OOXML; load/write rich OOXML; event-triggered label on document open. Their cross-platform and admin-deployment limitations are noted in [sample atlas](guides/OFFICIAL-WORD-SAMPLES-ATLAS.md).

**Substantive new guardrails:** the app-package guide is unified-manifest-specific; the sample's `OnDocumentOpened` code requires central admin deployment and is listed for Word Windows/web, not Mac sideload. Per-document auto-open uses AddInCommands 1.1 and doesn't cure missing global Developer Add-ins entries. The Hello World sample is suitable as a **separate** startup canary, not a replacement of ETK's manifest or a proof that all ETK features work. No live Mac Word test was conducted in this documentation update, so no product PASS/FAIL status changes follow from this review.

Outputs: [manifest decision guide](guides/PLATFORM-AND-MANIFEST-DECISION-GUIDE.md), [Word sample atlas](guides/OFFICIAL-WORD-SAMPLES-ATLAS.md), [Hello World canary runbook](troubleshooting/OFFICIAL-HELLO-WORLD-CANARY.md).

## Ribbon-first product architecture research — 2026-11-10

Reviewed current ETKPlus `mac-word-mvp/manifest.xml` and `public/taskpane.html` directly via authenticated GitHub. The former defines only one Word Home ribbon group and single `Paneli Aç` taskpane command, while HTML contains approximately **60 button elements** (including dialog controls) across numerous sections. Source architecture already separates `src/core` logic and `src/office` Word bridge. This supports *modular ribbon UI without rewriting Office.js mutators*, but it does **not** attest all 60 buttons work in Mac Word.

Linked Microsoft documentation verified and applied: [Add-in commands](https://learn.microsoft.com/en-us/office/dev/add-ins/design/add-in-commands) says one custom core tab/add-in with custom groups also permitted on built-in tabs, one-level dropdowns, six-group/seven-command design guidelines; [Navigation patterns](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns) separates quick ribbon commands from multi-step pane workflows; [Show/hide pane](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/show-hide-add-in) limits `showAsTaskpane()` to sideload/central deployment, **not Marketplace since Feb 23, 2026**; [custom keyboard shortcuts](https://learn.microsoft.com/en-us/office/dev/add-ins/design/keyboard-shortcuts) includes Mac Word 16.88+; [Word tracked changes](https://learn.microsoft.com/en-us/office/dev/add-ins/word/manage-tracked-changes) provides review APIs; [state persistence](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/persisting-add-in-state-and-settings) distinguishes document settings and browser storage.

Additional vendor sample index: [OfficeDev Office Add-in samples](https://github.com/OfficeDev/Office-Add-in-samples) contains [Office keyboard shortcuts](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-keyboard-shortcuts) (Word supported) and [Excel shared runtime ribbon/taskpane example](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/excel-shared-runtime-scenario) (**Excel only**; pattern not direct Word compatibility proof). [Yeoman overview](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/yeoman-generator-overview) still characterizes unified manifest for Word as preview/not production, unlike XML. This conflict between *host install support* and *production readiness* is explicitly recorded in the KB manifest decision guide.

Output: [Ribbon-first architecture](guides/RIBBON-FIRST-WORD-ADDIN-ARCHITECTURE.md), [ETKPlus capabilities and phased gaps](guides/ETKPLUS-CAPABILITIES-GAPS-ROADMAP.md), product [Issue #7](https://github.com/Centaurioun/ETKPlus-TransTools/issues/7). No modifications to live product, Word manifest, or active Codex PR #5 in this task.
