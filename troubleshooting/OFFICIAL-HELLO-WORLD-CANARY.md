# Isolated Microsoft Word Hello World canary for Add-in Error diagnosis

**Status: DOCUMENTED, NOT RUN IN THE USER'S MAC WORD.** Validated the official sample code and linked documentation on 2026-11-10. A canary (tiny reference add-in) identifies where a complex Office add-in startup pipeline fails. It is **not** a replacement for the product or a release acceptance test.

## When to use

- Word's Home → Add-ins shows a disabled/missing Developer Add-in, or the taskpane returns **Add-in Error** before any feature can be tested.
- `curl` to local HTML/icon returns 200 yet Word fails to load.
- An earlier app revision `.005` opened, while current `.007` sometimes fails. The latest 45-feature smoke run then yields **1 startup FAIL + 44 BLOCKED**, **not** 44 independently broken features.

### Why Microsoft Word Hello World?

[Official Hello World source](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world/word-hello-world) defines:
- XML taskpane manifest with `Host Name=Document`, ribbon `ShowTaskpane`, HTTPS SourceLocation, icons;
- taskpane HTML loading the Office.js CDN, then `Office.onReady` checking `Office.HostType.Word`;
- simple click handler calling `Word.run` / `context.sync`.

This is a reference control: if its stripped-down startup works in Word under comparable platform conditions, Word can load *at least that* Office.js app. It does **not** identify which ETK code change is faulty or prove all host capabilities.

## Non-negotiable isolation

1. **Only the main controller** operates the Mac Word UI. No parallel agent may edit active manifests, certificates, server processes, current Word document or shared port.
2. Save all work on ETK bugfix branch. Make a separate throwaway **worktree** for comparison; do not reset the live branch or replace the `main` tree.
3. Use a **new unique test manifest ID** for the canary, separate display label and separate HTTPS port (**not 3001**). Do not rename/remove other add-ins. A new GUID here is for the *new independent sample*, not a change to ETK's GUID. Record paths and cleanup plan.
4. Use synthetic blank Word doc; no real unpublished manuscripts, patient information or source fixtures.
5. Remove the sample's optional external telemetry `img` from `taskpane.html`. Never copy user content into sample logging. Check sample's external scripts/network traffic before launching.
6. Log exactly which Mac Word build is running, sample source commit, installed canary manifest path, TLS trust and canary HTML/JS asset hashes.
7. One owned test HTTPS server at a time where possible; if an ETK server must stay active, choose disjoint ports and positively identify each process. Do not kill unknown PIDs.

## Diagnostic steps

### Phase 0 — Read-only environment inspection
- Identify current installed XML manifests and which belongs to ETK; compare loaded `SourceLocation` against source; verify the exact taskpane and JS paths, not only root domain.
- Confirm currently **owned** ETK server on 3001, TLS certificate hostnames/trust and Word webview status. Do not assume HTTP 200 implies working Word API.
- Check [Mac sideload guide](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac), [Mac debugging guide](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac), and [initialize add-in](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/initialize-add-in).
- Create a safe backup/record of installed manifest entries (metadata/paths only, redact secrets).

### Phase 1 — Minimal canary
- Start isolated Microsoft Hello World code using safe own port/GUID; the sample default port is 3000 but other free port is allowed if **all manifest URLs/asset URLs** are updated consistently.
- Load the canary via the Word UI path, never by removing/replacing ETK.
- Record distinct events: ribbon tile visible; taskpane created; HTML rendered; Office.js script loaded; `Office.onReady` resolved with Word host; read-only button handler invoked. Only if separately approved on disposable doc, use the sample's optional **Say hello** mutation.
- Capture AX/screenshot or JavaScript diagnostics if available; distinguish blocked UI automation from observed host errors.

### Phase 2 — ETK version comparison (if canary works)
- Use exact last observed `.005` code commit `ff136c3eb30feb7d7ca5c3c295ddf656e24e1400`; current `.007` code at `90864a9ee1cfc002b292af77e38231509b8327d8`, later QA docs head may differ. Compare under the **same** Word/macOS host and exact active ETK manifest.
- Confirm *served* HTML and JS bundle digests / embedded runtime build ID for both; don't infer code from a static `v1.2` label or unrelated `build-info.js`.
- Run versions **sequentially**, not with two processes fighting over 3001. Preserve original dev worktree and report state.
- If `.005` PASS and `.007` FAIL under same conditions: test intermediate `7761c1c85c29d46653c37347e9f604087e9a2e5e` (startup build-identity gate) to narrow code regression.
- If both fail: investigate installed manifest, Word app host, sideload persistence, TLS, Office.js CDN, optional runtime logs and user account/deployment context before more code edits.
- If both work: record intermittent problem; repeat *one defined cold-reopen check* and then resume 45-feature inventory without escalating a speculative fix.

## Outcome matrix

| Canary | ETK .005 | ETK .007 | Interpretation / action |
|---|---|---|---|
| PASS | PASS | FAIL | Strong code/build-identity hypothesis; bisect the two commits |
| PASS | PASS | PASS | No reproducible startup regression now; record intermittent issue and continue smoke |
| PASS | FAIL | FAIL | ETK installed manifest/project hosting or app runtime likely; code/environment not yet isolated |
| FAIL | FAIL | FAIL | Common host/cert/sideload/environment candidate; canary itself also must be checked |
| PASS | BLOCKED | BLOCKED | App-specific observation prevented; don't invent a result |
| BLOCKED | any | any | Canary inconclusive; classify separately |

**False dichotomy warning:** some issues are document/account/session-specific. The matrix is diagnostic evidence, not mathematical proof of causality.

## UI/test closure
- Close disposable QA doc with **Don't Save** and verify closure. Never bulk-close all Word docs.
- Remove only the clearly identified **canary** manifest and stop only its known server if testing is over. Keep ETK manifest GUID, port, cache and certificates intact.
- No production app code changes, cache wipes or wholesale manifest conversion are part of the canary experiment.
- If a fix is required, write focused failing test, minimal code change and native Word verification. Preserve best known working version as reversible fallback.
- Annotate [Issue #6](https://github.com/Centaurioun/ETKPlus-TransTools/issues/6) and PR #5 with build-specific evidence, not a fabricated PASS for all 44 previously blocked feature checks.

## Relevant links

- [Official Word Hello World README and code](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world/word-hello-world)
- [XML manifest principles](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/xml-manifest-overview)
- [Mac Word sideloading](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac)
- [Initialization / Office.onReady](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/initialize-add-in)
- [Word command ribbon example](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word)
- [Office development errors guide](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors)
- [Upstream Office.js #6973](https://github.com/OfficeDev/office-js/issues/6973) reports sideload persistence regression; analogy only, not a known ETK-specific cause.
