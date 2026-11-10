# Office Word Engineering Agent Skill — six-cycle refinement and Scrum-quality acceptance

**Date: 2026-11-10 · Action:** revise `.agents/skills/office-word-addin-engineering/SKILL.md` as a reusable task router, keep long source references in KB, and add pressure cases. This is **documentation review**, not an actual Codex subagent evaluation.

## Cycle 1 — Trigger and scope audit

**Issue:** Old skill's metadata mentions mainly diagnosis and Mac WebKit quirks, despite now also supporting ribbon UI and academic editing design. It may fail to activate on "plan Word ribbon dropdown UX", "build an Office academic audit feature" or "manifest decision".

**Change:** Metadata explicitly names design, build, test and debug of **Word Office.js** add-ins, ribbon/dropdowns and taskpanes, document edits, manifest/API/runtimes and academic use. Negative scope: don't trigger merely to edit a standalone DOCX, analyze a clinical paper, or write a general web app.

## Cycle 2 — Source hierarchy and correctness

**Issue:** references in the previous SKILL.md overlap but don't state evidentiary priority. Agents may confuse GitHub issue anecdote with API contract or mock test with host evidence.

**Change:** Use hierarchy: official Microsoft Learn API/requirement set; Microsoft OfficeDev source samples; reproducible native Mac Word; KB case histories; community clues as hypotheses. Tag every claim `CONTRACT / SOURCE-INSPECTED / WORD-OBSERVED / HYPOTHESIS / BLOCKED`. Not all online code is tested on current host.

## Cycle 3 — Workflow and engineering roles

**Issue:** Original workflow is incident-first; product design decisions, Scrum backlog and feature discovery are implied rather than explicit.

**Change:** Route **Product design** separately from **host diagnostics**, **new feature implementation**, **document safety** and **QA**. Brainstorm alternatives and get user decision before implementing substantial UX changes. Avoid modal loops of debugging one feature when user asked to inventory all; a blocked host test does not make every feature FAIL. TDD and independent review for code changes. One owner for Word UI and localhost; analysis can run in parallel.

## Cycle 4 — Safety and compatibility

**Issue:** General skill needs explicit `ExecuteFunction` vs `ShowTaskpane`, shared-runtime and distribution caveats to avoid a broken ribbon implementation. Also needs to protect open academic/manuscript documents.

**Change:** Preserve preview+confirmation+final guard for mutations. No direct arbitrary Office.js from AI model on real user's Word document. XML ribbon/dropdown is enough for first prototype; do not force unified JSON. One custom ribbon tab, other groups optionally on built-in tabs. Host and marketplace capability gates. Treat references read-only where possible.

## Cycle 5 — Concision and portability

**Issue:** Direct relative links like `../../../guides/...` in a skill copied to another repository can break because the central KB files are not necessarily there.

**Change:** Keep `SKILL.md` concise with a portable local `references/WORKFLOW-ROUTER.md`, and link to central KB GitHub canonical URLs only as optional long reference. Make the skill usable offline on a repository clone. Don't copy whole Microsoft documentation. Do not install an external Office AI product to get a skill.

## Cycle 6 — Evaluate and govern

**Issue:** YAML parses and Codex discovered the old skill in a different session, but no behavior pressure test of the revised candidate has been run.

**Change:** Add `references/PRESSURE-TEST-CASES.md` with 8 independent cases and expected actions. Require baseline without skill (RED), new skill with independent agent (GREEN), revision (REFACTOR) when a harness permits. Static link/name/frontmatter checks are narrower. Document tested SHA and start new Codex session to verify actual loading. Keep previous skill SHA for rollback.

## Scrum-quality skill Product Backlog refinement

| Order | Proposed item | Acceptance criterion | Status |
|---|---|---|---|
| S-1 | Improve trigger and task routing | Named Word ribbon, host, safe edit and academic tasks match; generic DOCX editing not overtriggered | Designed |
| S-2 | Separate portable references | No project-root-relative links required for skill's critical instructions; local refs valid | Designed |
| S-3 | Explicit source and evidence taxonomy | Microsoft API contract not conflated with reported issue or Word observation | Designed |
| S-4 | Add pressure tests and evaluator record | >=8 scenarios incl ribbon, stale DOCX, blocked smoke, offline sources, privacy | Designed |
| S-5 | Runtime evaluation | New Codex session discovers exact new skill SHA; executes 8 scenarios against old/new and records behavior | **Pending—cannot claim from GitHub writes** |
| S-6 | Optional plugin packaging | Only if skill reuse warrants one; compare skill-only plugin in [OpenAI build docs](https://developers.openai.com/plugins/build/skills) | Deferred |

**Definition of Ready:** goal, tested baseline, sources, expected outcome, risk, independent evaluator available. **Definition of Done:** updated files, matching links, static schema validation, independently observed behavior test, rollback SHA, versioned commit. A document-only update may be called **published**, not **fully behavior-verified** until S-5.

This six-cycle method is our internal review practice; [Scrum Guide](https://scrumguides.org/scrum-guide) defines ongoing backlog refinement rather than an official Six-Cycle process.

## References

- [Our existing skill](../.agents/skills/office-word-addin-engineering/SKILL.md)
- [Research on existing skills](READY-SKILLS-AND-OFFICE-AGENT-ECOSYSTEM.md)
- [OpenAI build skills](https://developers.openai.com/plugins/build/skills)
- [Agent skills standard](https://agentskills.io/specification)
- [Superpowers writing-skills](https://github.com/obra/superpowers/tree/main/skills/writing-skills)
- [Scrum Guide](https://scrumguides.org/scrum-guide)
