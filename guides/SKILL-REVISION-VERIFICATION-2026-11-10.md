# Office Word engineering skill revision — verification record

**Date:** 2026-11-10. **Status:** UPDATED/PUBLISHED IN CENTRAL KB; RUNTIME BEHAVIOR EVALUATION NOT DONE. This log deliberately avoids claiming Codex already loaded the new skill.

## Before/after

- Previous `SKILL.md` Git blob SHA: `a9bfbfe849d6bd6c0a2f35fc3b4545b9747c3f99`.
- Revised `SKILL.md` Git blob SHA: `b597395f0aef92787e798c8b8a5bd8ae86900bf0`.
- Update commit: `f4b9b3133c71c89b24f9c3792350a9ea9924ccf8`.
- Skill name unchanged: `office-word-addin-engineering`.
- Trigger description extended from diagnostics to **design/build/debug/verify**, ribbon dropdown, permanent taskpane, academic tools and manifest decisions; explicitly excludes offline DOCX editing.
- Bundled local references:
  - [Task Router](../.agents/skills/office-word-addin-engineering/references/WORKFLOW-ROUTER.md)
  - [10 Pressure Cases](../.agents/skills/office-word-addin-engineering/references/PRESSURE-TEST-CASES.md)
- Central KB guidance: [six-cycle product review](SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md), [skill review](SIX-CYCLE-SKILL-REFINEMENT-2026-11-10.md), [ready skills](READY-SKILLS-AND-OFFICE-AGENT-ECOSYSTEM.md).

## Static verification actually executed

Using GitHub's exact `main` blobs, checked:
- YAML frontmatter separators present; skill `name` matches lowercase-hyphen regex; name under 64 characters.
- `description` nonempty and 283 characters (under 1024 Agent Skills standard limit).
- `SKILL.md` 51 lines.
- `agents/openai.yaml` present with interface metadata.
- Pressure scenario headings: **10**, not assumed passed.
- Bundled skill's **2 local relative reference links exist**.
- Across **7 newly authored/refined key documents**, 8 internal Markdown links were checked against current git tree; **0 missing**.
- Full repository link crawler, complete YAML-schema validation with official `skills-ref`, isolated Codex runtime discovery and old/new agent behavioral pressure testing **not performed** in this update.

## Explicit pending acceptance gates

1. In a **new, non-active Codex session**, inspect exact skill source Git SHA from central KB and compare with any existing local copy; don't overwrite a modified local skill.
2. Validate with `skills-ref validate` or the available official `$skill-creator` validator, recording actual output and tool availability.
3. Verify the new description is discoverable and explicitly invoke `$office-word-addin-engineering` for at least one benign product architecture and one host debugging task.
4. Run [10 pressure scenarios](../.agents/skills/office-word-addin-engineering/references/PRESSURE-TEST-CASES.md): baseline without skill (RED), independent fresh agent with updated skill (GREEN), refactor on actual misses. Different authors can write a case; the same evaluating agent must not see expected answer in context before baseline.
5. Check real Word development still sees its previous taskpane/build and that no P0 product files or installed manifest were touched as a side-effect of sync.
6. Record Codex version, skill path and SHA, pass/fail per case and rollback route. Only after this call the new skill **behavior verified**.

## Responsibility and safety

The central KB can be changed while another Codex task works in the *ETKPlus bugfix* worktree, but those sessions do **not** hot-reload private repo content unless explicitly linked and reloaded. Never claim skill sync to a live Codex session from a GitHub commit alone.

Do not overwrite current Word skill with third-party full AI add-ins. External sources are **references**, not install authorizations.
