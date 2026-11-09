# Office Add-in Engineering Knowledge Base

**Curated: 2026-11-10 · Scope: portable Word/Office.js engineering and troubleshooting**

> This knowledge base is a documented reference library, **not a proven fix bundle**. It records source URLs, observed symptoms, competing hypotheses, troubleshooting steps, risk levels and verification evidence. A passing mock/TypeScript test is never a real Word pass.

## Repository provenance

This standalone repository was curated from [`Centaurioun/ETKPlus-TransTools`](https://github.com/Centaurioun/ETKPlus-TransTools), branch `docs/office-addin-knowledge-base-20261110`. Its Markdown is original technical synthesis with links to upstream material; it does not redistribute source code or fixtures. The reusable Codex skill is at [`.agents/skills/office-word-addin-engineering/`](.agents/skills/office-word-addin-engineering/SKILL.md).

## Why it exists

ETKPlus × TransTools on Mac Word surfaced reproducible failures involving sideload persistence, startup, stale cached UI, false-positive OOXML snapshots, real stale-state rejection, UI confirmation, Range edits, Undo/Redo, and document import. These patterns recur in Office add-ins developed for other teams.

This directory is **portable**: it may be copied to a new standalone repository without copying the full ETKPlus application or private test documents.

## Where to start

| Symptom / task | Runbook |
|---|---|
| Fresh DOCX append falsely says document changed | [FILE-07 and OOXML snapshots](troubleshooting/FILE-07-OOXML-SNAPSHOTS.md) |
| Add-in missing at Word startup, loading spinner, server/manifest mismatch | [Mac startup and sideload](troubleshooting/MAC-SIDELOAD-STARTUP.md) |
| Stale preview still active, confirmation fails or runs twice | [Preview and confirmation security](troubleshooting/PREVIEW-CONFIRMATION-SECURITY.md) |
| Undo/Redo unavailable or multiple history units; ranges miss terminal matches | [Undo, Redo and Range](troubleshooting/UNDO-REDO-RANGE.md) |
| DOCX imports but styles/table/numbering drift | [DOCX import fidelity](troubleshooting/DOCX-IMPORT-INTEGRITY.md) |
| Need authoritative official documents or tools | [Curated source catalog](SOURCE-CATALOG.md) |
| Repeatable evidence and QA hygiene | [QA evidence workflow](workflows/QA-AND-EVIDENCE.md) |
| "Is Word running the new code?" | [Build identification and versioning](workflows/VERSIONING-AND-REPRODUCIBILITY.md) |
| Install/consume a shared Codex skill, MCP, tools | [Agent and skill integration](workflows/AGENT-AND-SKILL-INTEGRATION.md) |
| Move to an independent GitHub repository | [Codex migration prompt](prompts/CODEX-REPOSITORY-MIGRATION.md) |

## Case statuses on 2026-11-10

- **FILE-07:** FAIL — an untouched 208-character target is rejected before confirmation. Three text hashes match; three same-length OOXML hashes differ. First observed differing attribute: `w:rsidR`. **Not yet a proven sole root cause.**
- **FILE-08:** execution-time rejection PASS on changed target; early preview-button invalidation FAIL.
- **TXT-16:** preview/cancel/apply/Undo observed; Redo disabled in most recent run and formatting unverified.
- **TXT-04:** one run cleaned 12/12 punctuation spaces; an earlier run cleaned 11/12. Format and Undo/Redo remain unresolved.
- **Add-in startup:** port/manifest mismatch was repaired. Session-to-session disappearance may also align with Microsoft [issue #6973](https://github.com/OfficeDev/office-js/issues/6973), but local causality is not proven.
- **Product release:** PR #5 remains draft, not cleared. This repository is a knowledge capture and must not change those classifications.

## Evidence quality

- **A — Official contract:** Microsoft Learn API docs / standards; still version-and-host scoped.
- **B — Upstream verified report:** OfficeDev issue with status/build; may be fixed, or affect another host. Never generalize without reproducing.
- **C — Local empirical result:** versioned Word QA observation and hashes; applies only to tested fixture/build.
- **D — Community clue:** Reddit/Stack Overflow; use to form hypotheses, never as conclusive evidence.

Every troubleshooting note should carry **symptoms, applicability, competing causes, non-destructive diagnostics, fix criteria, regression matrix, sources, last checked**. Do not copy or redistribute other authors' source material. Link to it; bring only short original synthesis.

## Safety and confidentiality

Do not store real thesis text, patient data, private documents, tokens, API keys, raw OOXML or user's Word files here. Use synthetic fixtures; only share redacted structural diff, lengths, hashes and non-sensitive logs. Do not disable safety confirmation or perform direct mutation to "test" the backend. One disposable QA document at a time; close with Don't Save. Prefer private repository visibility while this remains tied to a private development project.

## Current skill provenance

The existing authored skill lives at `.agents/skills/office-word-addin-engineering/SKILL.md` and `.agents/skills/office-word-addin-engineering/agents/openai.yaml` on the parent Office tooling branch. Include those **unchanged** in any standalone knowledge repository, validate their discovery, and revise their runbook links to be portable. Do not confuse "YAML parsed" with "Codex runtime discovered the skill".
