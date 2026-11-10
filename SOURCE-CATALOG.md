# Curated Office Add-in resources

**Last checked: 2026-11-10.** Links, not copies. Prioritize official contracts over examples and anecdotes. See [source verification notes](SOURCE-VERIFICATION.md) for direct link/status checks and access limitations. Verify host (Word), platform (Mac), current Word build, API requirement set, last updated date, and issue status before relying on an item.

## Ribbon-first UI, commands, academic integration (2026-11-10)

Read [Ribbon-first architecture](guides/RIBBON-FIRST-WORD-ADDIN-ARCHITECTURE.md) and the [ETKPlus capability roadmap](guides/ETKPLUS-CAPABILITIES-GAPS-ROADMAP.md). These apply beyond a single Add-in Error: navigation, modular code, academic QA, packaging and UX.

| Official documentation | When useful | Important constraint |
|---|---|---|
| [Add-in commands](https://learn.microsoft.com/en-us/office/dev/add-ins/design/add-in-commands) | Ribbon architecture, groups, menus, custom tab | **One custom core tab per add-in**, one-level menus; can use built-in tabs |
| [CustomTab schema](https://learn.microsoft.com/en-us/javascript/api/manifest/customtab?view=word-js-preview) | XML custom ribbon tab | Avoid claiming two or three independent custom tabs |
| [Design navigation patterns](https://learn.microsoft.com/en-us/office/dev/add-ins/design/navigation-patterns) | Distinguish ribbon shortcuts vs focused pane | Deep links may require shared runtime |
| [Taskpane UX](https://learn.microsoft.com/en-us/office/dev/add-ins/design/task-pane-add-ins) | Replace long scrolling pane with focused navigation | Keep preview/results and complex options in pane |
| [Ribbon with XML manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/create-addin-commands) | ExecuteFunction vs ShowTaskpane | Preserve stable installed manifest IDs while testing |
| [Shared runtime](https://learn.microsoft.com/en-us/office/dev/add-ins/excel/configure-your-add-in-to-use-a-shared-runtime) | Shared ribbon/pane state, command dispatch | Additional runtime compatibility / configuration |
| [Show/hide task pane](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/show-hide-add-in) | Programmatic navigation | **Marketplace unsupported since 2026-02-23** for showAsTaskpane; sideload and central only |
| [Ribbon enable/disable](https://learn.microsoft.com/en-us/office/dev/add-ins/design/disable-add-in-commands) | Context-dependent buttons | Feature-specific support; restart may be needed |
| [Show/hide ribbon controls](https://learn.microsoft.com/en-us/office/dev/add-ins/design/show-hide-controls-custom-tab) | Declutter ribbon | RibbonApi 1.3/shared-runtime for dynamic visibility |
| [Word keyboard shortcuts](https://learn.microsoft.com/en-us/office/dev/add-ins/design/keyboard-shortcuts) | Fast academic checks, accessibility | SharedRuntime 1.1; Word Mac >=16.88; keyboard conflict review |
| [Persist add-in settings](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/persisting-add-in-state-and-settings) | Remember selected feature route/profile | Word per-document settings vs per-user browser storage; no private text |
| [Track changes in Word](https://learn.microsoft.com/en-us/office/dev/add-ins/word/manage-tracked-changes) | Academic edit traceability and editorial review | Check Word API availability; do not promise tracked-change editing automatically |
| [Word citation management](https://learn.microsoft.com/en-us/office/dev/add-ins/word/citation-management) | Reference bibliography and endnotes | Sample is not Zotero/CSL implementation |
| [Office Dialog API](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/dialog-api-in-office-add-ins) | Sign-in, progress, extra space | Separate runtime; document interaction better in pane |
| [Ribbon commands sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word) | Prototype custom tab and Home/Review groups | Use separate QA manifest/worktree |

## Microsoft Learn platform, manifest and app-package path (expanded 2026-11-10)

**Start with the [platform and manifest decision guide](guides/PLATFORM-AND-MANIFEST-DECISION-GUIDE.md)**; don't conflate the JSON unified app ZIP requirements with the current Word XML taskpane manifest.

| Topic / symptom | Official source | Decision for ETKPlus |
|---|---|---|
| Platform structure, manifest vs HTTPS-hosted app | [Office Add-ins overview](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/office-add-ins) | Essential architecture |
| JSON app ZIP, manifest and two icons | [App package for Microsoft 365](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/app-package-for-microsoft-365) | **Unified distribution** reference; does not require immediate conversion of XML |
| XML taskpane manifest | [Add-in only XML manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/xml-manifest-overview) | **Current production/development architecture**; SourceLocation, GUID, ordered XML, VersionOverrides |
| Unified JSON definition and compatibility | [Unified manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/unified-manifest-overview) | Mac Word/Excel/PPT >=16.103 support; consider only later |
| XML/JSON translation | [Compare manifest types](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/json-manifest-overview) | Future **planned** migration |
| Prevent duplicate XML and unified UI | [Manage two manifest variants](https://learn.microsoft.com/en-us/office/dev/add-ins/concepts/duplicate-legacy-metaos-add-ins) | Future deployment; alternates/hide |
| Startup and Office bridge | [Initialize add-in](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/initialize-add-in) | `Office.onReady()`, confirm Word host before Word.run |
| Mac XML sideload | [Sideload on Mac](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac) | Installed `wef` manifest vs source, Home → Add-ins |
| Mac JS console | [Safari Web Inspector](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac) | Requires supported Office installation; verify actual Inspect Element |
| API platform check | [Office versions and sets](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/office-versions-and-requirement-sets), [Word sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets) | Check feature support independently |
| Runtime optional feature gating | [Check API at runtime](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/specify-api-requirements-runtime) | Avoid unsupported event calls |
| Word editor implementation | [Completed Word tutorial](https://learn.microsoft.com/en-us/office/dev/add-ins/tutorials/word-tutorial) | Range, paragraph, table, image, content control |

## Microsoft Word sample atlas (expanded 2026-11-10)

See the [annotated official Word samples atlas](guides/OFFICIAL-WORD-SAMPLES-ATLAS.md) and [isolated Hello World canary troubleshooting](troubleshooting/OFFICIAL-HELLO-WORLD-CANARY.md).

| Official sample | When it helps | Key limitation |
|---|---|---|
| [Word Hello World](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world/word-hello-world) | **P0** distinguish Add-in Error/Office.js startup from app-specific failures | Run as separate GUID/port, no copying sample telemetry |
| [Hello World family](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world) | Cross-host architecture reference | Word cannot run other-host manifests |
| [Word ribbon commands](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word) | Ribbon disabled/missing, command JS, taskpane entry | Function-file runtime distinct from taskpane |
| [Auto-open taskpane](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/auto-open-task-pane) | Open an add-in with a **specific document** | Not a global sideload persistence fix |
| [Completed Word tutorial](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/tutorials/word-tutorial) | Smoke test and Word.range/text/table/image fixtures | Native Word validation needed |
| [Import template](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-import-template) | Compare DOCX import UX/API | Source example replaces document content; ETK appends |
| [BibTeX citation management](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-citation-management) | Academic citation picker, .bib parsing, endnote API | Not a Zotero/CSL replacement |
| [Get/edit OOXML](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-add-in-get-set-edit-openxml) | Read/structural diagnostic OOXML experiments | Legacy scaffold |
| [Load/write rich OOXML](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-add-in-load-and-write-open-xml) | Synthetic table/image/field content cases | Prefer modern typed Word APIs where supported |
| [Label on document open](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-add-label-on-open) | Later administrator-deployed event design | Listed for Word web/Windows; no Mac sideload event guarantee |

## Authoritative platform and API documentation

| Resource | Use when | Priority / caveat |
|---|---|---|
| [Word add-ins documentation](https://learn.microsoft.com/en-us/office/dev/add-ins/word/) | General architecture, import templates, Word lifecycle | A / primary |
| [Word JavaScript API](https://learn.microsoft.com/en-us/javascript/api/word?view=word-js) | Exact Word.run, Body, Range, Paragraph signatures | A / check requirement sets |
| [Word.Body](https://learn.microsoft.com/en-us/javascript/api/word/word.body?view=word-js) | DOCX import `insertFileFromBase64`, `getOoxml` | A / verify location/options |
| [Word.Range](https://learn.microsoft.com/en-us/javascript/api/word/word.range?view=word-js) | TXT-04 selection/search boundary and formatting | A |
| [Word requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets) | WordApi/WordApiDesktop supported on Mac host | A / no unsupported event assumptions |
| [Runtime requirement checks](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/specify-api-requirements-runtime) | Implement feature gates and fallback behavior | A |
| [Word events](https://learn.microsoft.com/en-us/office/dev/add-ins/word/word-add-ins-events) | FILE-08 early invalidation, paragraph/content-control events | A / events are opportunistic, not security authorization |
| [Create better Word add-ins with OOXML](https://learn.microsoft.com/en-us/office/dev/add-ins/word/create-better-add-ins-for-word-with-office-open-xml) | RSID, OOXML structure and import issues | A / RSID can matter for document combine; do not blindly strip |
| [Office add-ins platform concepts](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/office-add-ins) | Task pane/manifest/host separation | A |

## Microsoft official operational troubleshooting

| Resource | Use when | Caveat |
|---|---|---|
| [Troubleshoot development errors](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors) | Old JS/HTML still loaded; stale UI; silent property writes; manifest | Cache guidance is last-resort after build/URL check |
| [Mac debugging and Safari Inspector](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac) | UI events, Promise errors and JS diagnostics | Office.com install prerequisite |
| [Runtime logging on Mac](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/runtime-logging) | Add-in initialization/manifest loading error, not JS console | Enable briefly, disable after |
| [Validate manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-manifest) | GUID, SourceLocation, requirements, resource mismatch | `office-addin-manifest` as transient CLI |
| [Testing Office add-ins](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/test-debug-office-add-ins) | Native host test matrix, QA policy | Mock pass != native pass |
| [Known Office Add-in issues](https://learn.microsoft.com/en-us/office/dev/add-ins/resources/resources-office-add-in-known-issues) | Platform service incident triage | Mixes Outlook, Excel, deployment; filter by host |
| [Additional resources / support](https://learn.microsoft.com/en-us/office/dev/add-ins/resources/resources-links-help) | Microsoft Q&A, GitHub, Stack Overflow escalation | No automatic fixes |
| [Office add-ins glossary](https://github.com/OfficeDev/office-js-docs-pr/blob/live/docs/resources/resources-glossary.md) | Terminology, sideload, manifest, runtimes | Reference, not troubleshooting solution |

## Official code, prompts, scripts and tools

| Resource | Useful for | Decision |
|---|---|---|
| [Office-Developer-Tools](https://github.com/OfficeDev/Office-Developer-Tools) | Microsoft prompt/skill/agent/tool repository; currently mostly First-run Precheck | Watch/curate; do not bulk-install |
| [Microsoft Office Add-ins Copilot prompt library](https://learn.microsoft.com/en-us/office/dev/add-ins/resources/resources-github-copilot-prompt-library) | Word insertFileFromBase64, tasks, Office dialogs | Reuse ideas in Codex, never blindly rewrite |
| [Live prompt source](https://github.com/OfficeDev/office-js-docs-pr/blob/live/docs/resources/resources-github-copilot-prompt-library.md) | Versioned Markdown (2026-10-23) | Link + adapt |
| [Office-Addin-Scripts](https://github.com/OfficeDev/Office-Addin-Scripts) | Official npm tool collection | Select packages; not a Codex skill |
| [office-addin-manifest](https://www.npmjs.com/package/office-addin-manifest) | Validate XML without changing GUID | Recommended transient validation |
| [office-addin-debugging](https://www.npmjs.com/package/office-addin-debugging) | Server/sideload lifecycle | May alter working registration; isolate |
| [office-addin-mock](https://www.npmjs.com/package/office-addin-mock) | Mock API test pilot | Real host fidelity not guaranteed |
| [office-addin-test-server](https://www.npmjs.com/package/office-addin-test-server) & [test-helpers](https://www.npmjs.com/package/office-addin-test-helpers) | Structured add-in harness | Pilot only when test contract designed |
| [Office add-in samples](https://github.com/OfficeDev/Office-Add-in-samples) | Task-pane Word import, OOXML and citations | Adapt small examples; never wholesale replace |
| [Word import-template sample](https://learn.microsoft.com/en-us/samples/officedev/office-add-in-samples/word-import-template/) | insertFileFromBase64 usage | Sample may replace body; our case must append |
| [Get/edit/set OOXML sample](https://learn.microsoft.com/en-us/samples/officedev/office-add-in-samples/word-get-set-edit-ooxml/) | Diagnostic XML structure | Synthetic docs only |
| [office-js-snippets](https://github.com/OfficeDev/office-js-snippets) | Minimal tested Script Lab snippets | Check API set |
| [Office.js docs authoring](https://github.com/OfficeDev/office-js-docs-pr/tree/live) | Latest authored guides | Source for Microsoft Learn |
| [Office.js reference source](https://github.com/OfficeDev/office-js-docs-reference) | API reference metadata | Prefer rendered Learn where possible |
| [Office.js issue tracker](https://github.com/OfficeDev/office-js/issues) | Host regressions and known bugs | Verify open/fixed status and host |
| [Microsoft Learn MCP](https://learn.microsoft.com/en-us/training/support/mcp-developer-reference) | Codex documentation lookup | Configured != queried; verify runtime |
| [Script Lab](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/explore-with-script-lab) | Small isolated Office.js experiments | Never use production docs |

## Directly relevant upstream issues

| Issue | Affects | Status checked 2026-11-10 |
|---|---|---|
| [#6973](https://github.com/OfficeDev/office-js/issues/6973) | Sideloaded Word ribbon add-in disappears after restart on Mac/Windows | **Open / under investigation**; strong analogy, not local root-cause proof |
| [#6597](https://github.com/OfficeDev/office-js/issues/6597) | Office Add-ins loading spinner and empty store for work accounts | **Open**; account-type specific |
| [#4078](https://github.com/OfficeDev/office-js/issues/4078) | getOOXML nondeterministic `w14:paraId` | **Closed July 2026** (Microsoft says fixed); our observed `w:rsidR` is different |
| [#6703](https://github.com/OfficeDev/office-js/issues/6703) | insertFileFromBase64 hierarchical list indentation lost | **Closed June 2026 / offending change turned off per maintainer comment**; recheck import fidelity on the target build |
| [#2225](https://github.com/OfficeDev/office-js/issues/2225) | Range.expandTo may add Undo history units | **Closed not planned** (older Word on web report) |
| [#1827](https://github.com/OfficeDev/office-js/issues/1827) | Request for an Office.js UndoRecord equivalent | Closed May 2021; Microsoft said it had no plans to add the capability at that time. This is a historical response, not a current roadmap statement; do not promise one Undo step. |
| [#6952](https://github.com/OfficeDev/office-js/issues/6952) | Store add-in error (primarily Windows) | **Fixed Sept 2026**; not same as Mac dev sideload |

## Community / lower-confidence clues

| Source | What it suggests | Evidence grade |
|---|---|---|
| [Reddit r/Office365: add-ins failing to load](https://www.reddit.com/r/Office365/comments/1qytlc8/serious_error_loading_addins_dont_know_what_to_do/) | Multiple users reported intermittent Office loading problems | D / anecdote; not an ETK diagnosis |
| [Reddit r/Office365: account sign-in linked add-in load failures](https://www.reddit.com/r/Office365/comments/1trr1up/can_no_longer_use_addins_in_ms_office_365_apps_or/) | Potential account/backend interactions | D / Windows/web reports, not Mac fixture proof |
| [Stack Overflow: atomic Word Undo not available](https://stackoverflow.com/questions/66388993/office-word-add-in-js-how-to-avoid-actions-taken-by-the-add-in-to-appear-in-th) | Word mutations may create multiple history units | C/D / older community Q&A |
| [Microsoft Community Learning video: build your own AI in Office](https://www.youtube.com/watch?v=jbCfs7VQY3A) | ToolRegistry, context resolver, Word event handlers, AI architecture | D for correctness; architectural inspiration only |

## Excluded or deferred

- [Office Add-ins Development Kit](https://github.com/OfficeDev/OfficeAddinDevKit): available Microsoft tool; adoption deferred until current project needs and support are reviewed. Do not confuse Visual Studio add-in project deprecation with this VS Code kit.
- Microsoft 365 Agents Toolkit / SSO examples: not relevant to current local-only P0 faults.
- COM/VSTO and Windows-specific WAM/WebView2 fixes: **different architecture**; do not apply to Mac Word Office.js.
- Arbitrary third-party MCP servers promising "Word editing": editing DOCX in filesystem is not the same as debugging a live Office.js task pane.

**Review cadence:** revisit upstream status before P0 retest and on Word host upgrade; record the issue's latest update and whether it reproduces on our actual build.
