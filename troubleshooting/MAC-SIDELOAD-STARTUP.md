# Mac Word Office.js — Add-in absent, disabled, stuck Loading, "Add-in Error"

**Scope:** Word for Mac XML-manifest developer sideload, local HTTPS task pane. Use for unrelated future add-ins too.

**Applicability / last checked (2026-11-10):** Word for Mac, Office.js add-ins. Exact Word/Office build for this case: not recorded in the source evidence; do not generalize to other builds without retesting.
## Symptom matrix

| Symptom | Top hypotheses | First non-destructive measurement |
|---|---|---|
| Add-in disappears after quitting/reopening Word | Host-side persistence regression; document-specific activation; manifest registration | Check upstream [Office.js #6973](https://github.com/OfficeDev/office-js/issues/6973) **open** as of 2026-11-10; record exact version/build |
| Home → Add-ins shows Loading for a long time | Store/identity/back-end issue; network; Office account/tenant | Compare personal/work account only if already available; [#6597](https://github.com/OfficeDev/office-js/issues/6597) open; don't assume ours is same |
| Developer Add-in entry disabled / task pane won't open | Word's add-in host, manifest URL/certificate, sideload | Record selected tile, exact UI state, host logs, verify full HTTPS taskpane/JS/icon URLs |
| Add-in Error | Installed manifest vs current source/port; host policy/service incident; JS bootstrap failure | Record exact error and Word version; check [Microsoft troubleshooting](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors) |
| Browser curl 200, but Word pane fails | WebView trust, cached or wrong manifest, JS runtime error | HTTP 200 proves local reachability **only** |
| After build Word still looks old | Static HTML label, old JS still cached, wrong process/worktree | Compare **runtime embedded build ID + SHA + served artifact digest** |

## Case-specific evidence

ETKPlus observations, local UI steps, and the 3001/3002 port history are kept in the [ETKPlus case guide](../cases/ETKPlus/README.md). Do not apply them to another project without reproducing the conditions.

## Decision tree

1. Identify the **host**: Word Mac, OS and build, account/tenant category (without credentials), add-in GUID, manifest version, taskpane location, selected command, exact UI error.
2. Inspect the **server owner**: actual PID, command-line, worktree, localhost port and path. Never terminate unrelated processes. Check TLS trust within Word; test taskpane HTML, JavaScript, CSS and icon URLs rather than only HTML.
3. Inspect the **installed manifest** (not just source): SourceLocation, logo routes, ribbon commands, permissions, GUID; compare it with checked-in `manifest.xml`. Do not change GUID or replace other sideloaded manifests.
4. Inspect **build identity**: if `v1.2` is hardcoded HTML, it proves nothing. Require a build token embedded in actual compiled JS. A process running on port 3001 is not proof that it serves latest bundle.
5. If server/manifest/digest match but host still refuses, check the official [Office troubleshooting](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors), [Mac Web Inspector guide](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac), and [host runtime logs](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/runtime-logging) **only for load/manifest errors**. Runtime logging doesn't include JS console output.
6. **Mac runtime logging (temporary only)** per Microsoft: `defaults write com.microsoft.Word CEFRuntimeLoggingFile -string "runtime_logs.txt"`, restart Word, inspect `~/Library/Containers/com.microsoft.Word/Data/runtime_logs.txt`, then revert with `defaults delete com.microsoft.Word CEFRuntimeLoggingFile`. Log may contain other add-in metadata: review/redact before sharing. Save existing setting before changing.
7. Safari Web Inspector requires a supported Word installation from Office.com rather than Mac App Store, and `defaults write com.microsoft.Word OfficeWebAddinDeveloperExtras -bool true`. Check actual **Inspect Element** availability; setting true ≠ inspector verified. Restore previous setting if necessary.
8. Use cache-clearing only with **documented scope and explicit rollback plan**. Microsoft lists cache remediation, but wiping all Office user data or WEF blindly may affect other add-ins and won't fix #6973 by itself.
9. If host regression remains likely, log as **BLOCKED (HOST)** with evidence and compare to a clean profile/host only if isolated; do not call it a product regression without code-path evidence.

## Required regression

- Brand-new blank Word document: discover/load add-in via approved route.
- Quit/relaunch Word: record if developer add-in persists, version/GUID matched, whether reactivation required.
- Stop owned server: expected controlled failure; restart it: recover without changing manifest.
- Rebuild changed code: verify **new JS-embedded build ID**, not just changing static HTML.
- No unrelated manifest, process or user document modified.

## References

- [Office.js #6973 sideload persistence](https://github.com/OfficeDev/office-js/issues/6973) — OPEN and internally assigned as of 2026-11-10.
- [Office.js #6597 loading menu stuck](https://github.com/OfficeDev/office-js/issues/6597) — OPEN, account-scoped reports.
- [Office.js #6952 marketplace load failures](https://github.com/OfficeDev/office-js/issues/6952) — fixed Sept 2026, different baseline.
- [Mac debugging official](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac)
- [Host runtime logging official](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/runtime-logging)
- [Microsoft troubleshoot development errors](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors)
- [Reddit add-ins sporadic loading](https://www.reddit.com/r/Office365/comments/1qytlc8/serious_error_loading_addins_dont_know_what_to_do/) — anecdotal; no confirmed fix for our Mac sideload.
