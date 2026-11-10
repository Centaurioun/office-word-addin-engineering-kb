---
name: office-word-addin-engineering
description: Diagnose, implement, and verify Microsoft Word Office.js task-pane behavior, including Word API compatibility, Mac WebKit quirks, document-safe range edits, manifests, and real Word regression testing.
---

# Office Word add-in engineering

Use this skill for Microsoft Word Office.js add-ins across projects, including task panes on Mac and other supported hosts. Prefer Microsoft Learn and the OfficeDev repositories linked below for API claims. Do not infer real Word behavior from a mock or browser-only test.

## Diagnosis and change workflow

1. Classify the failure (startup/manifest, host API, range/selection, UI confirmation, stale preview, document integrity, or test harness). Record the exact add-in commit and Word/macOS version.
2. Find the API member and its requirement set in the [Word JS API reference](https://learn.microsoft.com/en-us/javascript/api/word?view=word-js). Check current Mac minimum version/build for `WordApi` and `WordApiDesktop` in [Word API requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets); gate optional members with `Office.context.requirements.isSetSupported` and test setless features safely.
3. Inspect the relevant implementation, callers, and focused regression test before editing. Make the smallest change that preserves existing selection scope, formatting, tables, fields/OOXML, and error handling.
4. For document mutations, use `Word.run`, queue range-scoped work, and `context.sync` at deliberate read/write boundaries. `Range.search` returns Word ranges; verify their actual boundaries in the target host before editing adjacent or end-of-paragraph text. Use `insertText` with a deliberate `Word.InsertLocation`; do not replace a whole story to fix a local range defect.
5. Treat preview as a snapshot, not authorization. Before applying, revalidate document identity/content, selection, source file, and preview eligibility; reject stale state. In the task pane, show the target, impact, warnings, and separate Cancel/Confirm controls. Lock repeat submission and keep errors visible.
6. Run focused automated tests, then the affected real Mac Word test on a disposable synthetic DOCX. Inspect actual before/after content and formatting, cancel behavior, Word Undo/Redo, and save/reopen only when required. Close only the QA copy without saving. Never claim a mock pass as real Word evidence.
7. For startup failures, inspect the exact XML manifest ID/URLs, installed sideload copy, server owner/port, HTTPS trust, and static asset responses. Use the project runbook's Developer Add-ins route; do not clear all Office caches, overwrite other manifests, or run an orchestrator that can reconfigure the live sideload without an isolated plan.
8. Use Safari Web Inspector only for observing/debugging the task-pane runtime. Never bypass application confirmation, preview validation, or document safety gates from the console.
9. Record each issue using the [troubleshooting template](../../../templates/TROUBLESHOOTING-CASE-TEMPLATE.md): symptom, environment/version, evidence, competing causes, sources, safe diagnosis, fix, regression, and outcome. Include the applicable Word/Office version and last checked date; write `unknown` when evidence is unavailable.

For platform/manifest questions first read [platform and manifest decision guide](../../../guides/PLATFORM-AND-MANIFEST-DECISION-GUIDE.md). For code patterns or a minimal independent reference use [the OfficeDev Word samples atlas](../../../guides/OFFICIAL-WORD-SAMPLES-ATLAS.md). If a Mac Word **Add-in Error** blocks all feature tests, follow [the isolated Hello World canary runbook](../../../troubleshooting/OFFICIAL-HELLO-WORLD-CANARY.md), then compare last verified working app version against failing app version under matched conditions. A sample must have its own GUID/HTTPS port and must not include optional telemetry or access real user documents. Do not confuse per-document auto-open with developer add-in persistence.

## Reusable procedures

Before native QA, follow the [QA and evidence workflow](../../../workflows/QA-AND-EVIDENCE.md) and the matching troubleshooting record. Project-specific sideload paths, fixtures, branch names, and UI walkthroughs belong in a project case guide, not this general skill. Use disposable synthetic documents and never use real user documents as test inputs.

## Authoritative references

- [Microsoft Learn MCP server](https://learn.microsoft.com/en-us/training/support/mcp-developer-reference) — use its `microsoft_docs_search` / `microsoft_docs_fetch` tools for focused, non-sensitive Office.js queries when available; never send document or account content.
- [Word JavaScript API reference](https://learn.microsoft.com/en-us/javascript/api/word?view=word-js), [Word.Range](https://learn.microsoft.com/en-us/javascript/api/word/word.range?view=office-js), [runtime requirement checks](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/specify-api-requirements-runtime), [Word requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets).
- [Debug Office Add-ins on Mac](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac) — Safari Web Inspector prerequisites and interaction.
- [OfficeDev/Office-Addin-Scripts](https://github.com/OfficeDev/Office-Addin-Scripts) — manifest, mock, test-server/helpers, debugging, and lint package docs.
- [OfficeDev/Office-Add-in-samples](https://github.com/OfficeDev/Office-Add-in-samples) and [office-js-snippets](https://github.com/OfficeDev/office-js-snippets) — focused examples; verify each sample's API-set metadata.
- [office-js-docs-reference](https://github.com/OfficeDev/office-js-docs-reference) and [office-js-docs-pr](https://github.com/OfficeDev/office-js-docs-pr) — generated API reference and authored documentation sources.
- [OfficeDev/office-js](https://github.com/OfficeDev/office-js) — issue tracker and runtime repo; issue reports are evidence of a reported case, not universal platform guarantees.
