> **Historical prompt retained for provenance.** The migration it describes is complete in this repository; do not rerun its copy/create/push instructions without a new explicit request.

# Codex instruction: create and populate reusable Office Add-in knowledge repository

> Use this only after source knowledge branch was reviewed. Repo creation requires the user's GitHub login and explicit project scope. **Default to PRIVATE visibility**, never expose private QA fixtures or secrets.

You are Codex with local access to `/Users/yusuf/Repos/ETKPlus-TransTools-BUGFIX-20261109` and the GitHub repository `Centaurioun/ETKPlus-TransTools`.

**Goal:** create an independent, portable repository named `Centaurioun/office-addin-engineering-kb` for development sources, troubleshooting playbooks, the existing Codex Office Word skill, and future Office add-in case studies. This repository is NOT a new application and should contain no real user documents.

## Procedure

1. Check `gh auth status` (without leaking token) and whether the target repository exists already. If it exists, inspect ownership, visibility and contents and avoid overwriting; reconcile by adding files in a feature branch.
2. Fetch original staging branch `docs/office-addin-knowledge-base-20261110` from `Centaurioun/ETKPlus-TransTools`. Locate `knowledge-base/` docs, `.agents/skills/office-word-addin-engineering/SKILL.md` and companion `agents/openai.yaml`, and existing `docs/qa/OFFICE_ADDIN_TOOLING_EVALUATION.md`.
3. Create new private local repository only if it does not already exist. Copy curated Markdown files from `knowledge-base/*` **to root** so index links remain valid, then copy authored skill to `.agents/skills/office-word-addin-engineering/`. Keep author/provenance links to source repo and commit.
4. Make skill portable: existing skill points to ETK-only `docs/qa/CODEX_MAC_RUNBOOK.md`; replace with portable `workflows/QA-AND-EVIDENCE.md`. Move project-specific Word UI steps and fixture paths into `cases/ETKPlus/README.md`. Keep a pointer back to upstream project **without copying its private docs or QA binaries**.
5. Keep `OFFICE_ADDIN_TOOLING_EVALUATION.md` as a **redacted case study** under `cases/ETKPlus/`, with no tokens, personal identifiers or machine-specific private content. Do not copy user data or original test fixtures.
6. Add a license choice only after reviewing upstream licenses; do not copy Microsoft copyrighted manuals/code, just links and original analysis. Verify Markdown links, all referenced local files, no accidental credential strings, and no large binaries.
7. If authenticated `gh repo create Centaurioun/office-addin-engineering-kb --private --source <local-dir> --push` is available, create repo; otherwise leave complete local repo initialized and report the exact inability. Do **not** silently make public.
8. Make an initial import commit; provide canonical GitHub URL, commit SHA, file list and source-staging branch SHA. No automated PR merges to current ETK app. Avoid changing ETK bugfix worktree.
9. Verify Codex skill discovery in a fresh session. Verify Microsoft Learn MCP with a **real** query (config alone not enough), but do not block repo creation if unavailable.
10. In ETKPlus repo, keep only a short reference link to central knowledge repo and pin an exact skill/source version if vendored. Keep `PR #5` draft and no product code changes.

## Ongoing intake template

Each newly found issue needs a short original note: symptom, host/version, first observed date, source authority, current upstream status, reproducibility, privacy-safe diagnostic, acceptance regression, and a next-review date. Use issue tracker references as *hypotheses*, not fixed universal truths.

After initial repo creation, return to ETKPlus `FILE-07` root-cause work only once the repository and provenance checks are complete. Do not restart 130-case QA.
