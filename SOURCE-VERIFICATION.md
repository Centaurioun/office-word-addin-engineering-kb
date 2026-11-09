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
