---
name: office-word-addin-engineering
description: Design, implement, troubleshoot and verify Microsoft Word Office.js add-ins (not offline DOCX editing). Use for ribbon tabs, dropdown menus, taskpanes, academic editing tools, Mac Word sideload, XML/unified manifest decisions, safe Word.run edits, API compatibility and real Word QA.
---

# Microsoft Word Office.js engineering

Use this reusable skill when building or reviewing **Word Office.js add-ins** across projects. It supports both **product architecture** and **incident diagnosis**. Do not trigger for generic academic writing, standalone Word document proofreading, offline DOCX conversion, or unrelated web application design.

**Read [the bundled task router](references/WORKFLOW-ROUTER.md) before selecting an engineering path.** It works without access to the private central KB. For skill maintenance, read [the pressure-test suite](references/PRESSURE-TEST-CASES.md); those scenarios require real evaluation and are not already passed.

## Establish facts before proposing change

1. Identify the user's actual goal: new Word feature, ribbon+pane architecture, feature inventory, host startup, Range/OOXML error, manifest/packaging, academic feature, QA or skill revision. Do **not** force an incident-debugging workflow onto product architecture requests or endlessly revisit one defect during a requested whole-feature survey.
2. Inspect current code/manifest, exact **tested** Word/macOS build, installed manifest, served JS, build token, target fixture and requirement sets. Distinguish `declared`, `implemented`, `unit-tested`, `Word-observed` and `blocked`.
3. Use this evidence hierarchy: **Microsoft Learn API contract and requirement sets → OfficeDev reference/sample source → reproducible native Word observation → upstream bug report → community clue**. Mark `HYPOTHESIS` vs `WORD_OBSERVED`; never infer native support from TypeScript compilation or mocked Word calls.
4. When actual Microsoft Learn MCP tools are available, make a real docs search/fetch for relevant API signatures and host compatibility. `codex mcp get` alone proves only configuration. If unavailable, use official docs and report the limitation.

## Product and UX decisions

- Treat **ribbon and right-hand taskpane as complementary, permanent surfaces**: ribbon for visible command groups, one-level `Menu` dropdowns and shortcuts; pane for focused feature settings, long results, preview and explicit approval. Do not force the current panel to be removed or move 60 buttons to ribbon by default.
- Microsoft Word add-in commands permit **one custom core tab per add-in** and additional groups on built-in Word tabs. Prototype dropdowns with the [official Word command sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word); verify actual Mac Word behavior. Do not promise nested menus or several custom tabs.
- Preserve existing `src/core` pure functions and `src/office` Word adapters. Define stable **feature IDs**, requirement sets, scope, preview/confirm policy, SMK/QA mapping and **one semantic dispatcher**. Ribbon and pane call the same implementation; don't fork document mutations.
- **XML and unified JSON are alternatives, not freshness scores.** Existing XML supports a custom tab, buttons and dropdowns; don't migrate solely for ribbon. Consider JSON only against platform compatibility, deployment targets and its current production-readiness documentation.
- Understand action lifecycles: `ShowTaskpane`, `ExecuteFunction`, `Office.addin.showAsTaskpane()` and shared runtime differ; confirm Marketplace restrictions before designing deep-link workflows. Prefer an isolated two-group, 3–5-command XML pilot while keeping the existing Home open-pane command.
- For meaningful new UX, use brainstorming alternatives and a design specification **before coding**. Six-cycle product refinement and continuous Scrum Product Backlog refinement are complementary methods, not substitutes for actual native Word testing. See the [central product refinement](https://github.com/Centaurioun/office-word-addin-engineering-kb/blob/main/guides/SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md).

## Build and modify document behavior

1. Check the relevant [Word API reference](https://learn.microsoft.com/en-us/javascript/api/word?view=word-js), [Word requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets) and runtime `isSetSupported` checks. Word Mac, Word web and Excel samples are **not interchangeable**.
2. For Word changes, queue scoped `Word.run` work and deliberate `context.sync` calls; verify `Range.search` boundaries and preserve styled runs, tables, fields, citation managers, images, selection and document identity. Don't replace an entire story to repair a local whitespace problem.
3. Keep preview **read-only**; for mutating tools show scope and proposed change, require explicit Cancel/Confirm, prevent duplicate submissions, and revalidate target/source state **inside the Word.run that applies the change**. Fail closed on uncertain OOXML or unsupported API. Never directly invoke a mutator from DevTools to skip confirmation.
4. If a reported issue is reproducible, use systematic debugging, add a focused failing regression, apply the smallest justifiable fix, and verify negative cases. If the task is a smoke inventory, test each feature once and classify `COMPLETE`, `FAIL`, `BLOCKED`, `NOT_SUPPORTED` or `NOT_RUN` without treating a shared startup failure as many feature failures.
5. For academic tooling, distinguish heuristic findings from validated reference/citation/statistical errors. Do not rewrite Zotero/Word fields without explicit feature support, and do not transmit confidential academic/patient documents to external AI or telemetry without explicit authorization.

## Mac Word host and actual QA

- For startup: verify **installed** manifest ID/URLs, owner of localhost server/port, HTTPS certificate, all loaded JS/CSS assets, `Office.onReady`, and exact JS-embedded build identity. HTTP 200 and a static `v1.2` label do not prove the taskpane executed. Compare last truly working vs failing build under controlled conditions.
- If useful, run an **isolated** official Word Hello World canary with its **own GUID/port**, no optional telemetry and a synthetic blank document. Do not touch another add-in's manifest, server or user's Word documents.
- Test changes in **real Word**, one disposable synthetic copy at a time: preview, cancel, approve, exact content and formatting, Undo/Redo and saved/reopened copy when relevant. Close each ordinary QA copy with Don't Save. Mock tests, manifest validators, and native Word observations are **different evidence layers**.
- Record `app build/token, commit/dirty source, served asset hash, Word/macOS version, fixture checksum, test ID, observed result, evidence and limitations`. Keep PR draft/merge state and release gate explicit; a successful unit suite does not certify product acceptance.
- For independent subproblems, dispatch read-only analysis subagents when available; only **one** controlling agent owns live Word, installed manifests and servers. Do not claim subagent or skill use that did not happen.

## Reuse, governance, and further reading

The [central Knowledge Base](https://github.com/Centaurioun/office-word-addin-engineering-kb) contains long-form sources, runnable diagnostic decision trees, sample atlas, ribbon UX and Scrum backlog. Use its pages if available, but this skill must still function with the local router alone.

For skill changes: use `superpowers:writing-skills` and `$skill-creator` when available, check [Agent Skills specification](https://agentskills.io/specification) and [OpenAI build-skills guidance](https://developers.openai.com/plugins/build/skills), run RED/GREEN pressure scenarios in a **fresh** Codex session, and retain the previous Git SHA for rollback. YAML validation ≠ runtime discovery or behavioral verification.

For technical questions, prefer Microsoft [Word docs](https://learn.microsoft.com/en-us/office/dev/add-ins/word/), [OfficeDev sample catalog](https://github.com/OfficeDev/Office-Add-in-samples) and [OfficeDev scripts](https://github.com/OfficeDev/Office-Addin-Scripts). External Office AI agent code with arbitrary Word execution should be studied as architecture only, not installed into a confidential document editor without a separate safety design.
