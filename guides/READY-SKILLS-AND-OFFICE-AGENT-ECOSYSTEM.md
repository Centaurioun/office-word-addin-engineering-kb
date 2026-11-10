# Reusable skills and Office agent resources — curated evaluation

**Checked 2026-11-10.** Purpose: find existing assets that can make a Codex Word Office.js developer stronger without blindly installing a new automation stack. Distinguish **Agent Skill**, **prompt/sample**, **MCP tool**, **runtime application**. No unreviewed repository was installed; no code or telemetry copied.

## Decision table

| Source | What actually exists | Best use | Adoption decision |
|---|---|---|---|
| [OfficeDev/Office-Developer-Tools](https://github.com/OfficeDev/Office-Developer-Tools) | Microsoft Office AI tool collection; as checked, First-run Precheck is the substantive extended prompt; Outlook Mac pinning script is unrelated | First-run Marketplace-readiness checklist | **Reference/borrow pattern**, not a full Word engineering skill |
| [OfficeDev Word ribbon sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word) | XML manifest `Control xsi:type="Menu"` with dropdown items, `ShowTaskpane` and `ExecuteFunction` | Verify Mac ribbon menus and action wiring, source for isolated pilot | **High value**, port minimal patterns after license and API review |
| [Office Add-ins Copilot prompt library](https://learn.microsoft.com/en-us/office/dev/add-ins/resources/resources-github-copilot-prompt-library) | Microsoft-written task prompts; e.g., DOCX import | Task-specific prompts with official APIs, citation to docs | **High value reference**, not a skill package |
| [microsoft/skills](https://github.com/microsoft/skills) | Microsoft Agent Skills ecosystem, mostly Azure/Foundry SDKs, agent/AGENTS templates | Learn structure, tool routing, reference separation and validation | **Skill engineering pattern only**; not a proven Word Office.js skill |
| [openai/plugins](https://github.com/openai/plugins) | Current OpenAI plugin distribution and skill examples | Build skill-only plugins; scope and evaluation examples | **Authoring reference** for later optional packaging |
| [openai/skills](https://github.com/openai/skills) | Old Codex skills catalog; README marks repo **deprecated** | Historical inspiration only | **Don't adopt as current main distribution** |
| [OpenAI Build Skills](https://developers.openai.com/plugins/build/skills) | Official build instructions, progressive disclosure, dependencies, testing | Revise our `SKILL.md` and build references | **High value, use** |
| [Agent Skills specification](https://agentskills.io/specification) | Open standard `SKILL.md` YAML/name/description, relative resource references, validation | Static conformity and portable structure | **High value, use** |
| [Superpowers](https://github.com/obra/superpowers) | Agent process skills: brainstorming, systematic-debugging, TDD, writing-skills, worktrees, review/verification | Govern design and skill improvement; avoid unstructured endless code debugging | **Invoke when installed**, do not copy/relicense wholesale |
| [hewliyang/office-agents](https://github.com/hewliyang/office-agents) | A **full third-party Office add-in product**, with Word `AppAdapter`, tool registry, local Office bridge, OOXML/text/structure readers, agent skills configuration | Study abstract **AppAdapter**, tool boundaries, Word runtime inspection | **Research only**. README says not production-ready or intended for Microsoft Add-in Store. Raw `execute_office_js` tool and BYOK external LLMs are not suitable to copy into a private manuscript editor without safety design |
| [Scrum Guide](https://scrumguides.org/scrum-guide) | Official 2020 continuous backlog refinement, Product Goal, Definition of Done | Order work, slice incremental value, avoid huge features | **Planning method**, not a Codex-specific technical skill |
| [Microsoft Office.js reference](https://learn.microsoft.com/en-us/office/dev/add-ins/word/) + [Learn MCP](https://learn.microsoft.com/en-us/training/support/mcp-developer-reference) | Primary API contract and targeted search | Word syntax, requirement sets, true host behavior | **Primary authoritative documentation** |

## What not to mistake for reusable Codex skills

- OfficeDev **Word Hello World** and ribbon samples are **source code examples**, not a pretested agent skill. They can inform a skill's referenced workflow but don't replace Word native QA.
- A Word add-in containing an AI tool registry (e.g. `office-agents`) is a different **product/extension**, not automatically a safe skill for Codex. Running arbitrary Office.js via an AI agent can affect open documents.
- The deprecated `openai/skills` repo should not be the preferred reference when [OpenAI's current plugins documentation](https://developers.openai.com/plugins/build/skills) and [openai/plugins](https://github.com/openai/plugins) are available.
- Superpowers **brainstorming** is decision exploration and design review; it does not automatically prove an implementation works in Word.
- A static YAML parse is not an actual Codex skill discovery or behavior test. Verify each in the consuming runtime.

## What our custom skill genuinely adds

No verified ready-made skill in this review combines **Mac Word native host diagnosis + Word.Range/OOXML + taskpane/ribbon commands + safe confirmation + academic QA + bugfix vs product architecture routing**. Therefore maintain our own skill, backed by Microsoft Learn and official code examples. Keep it **small and reusable**, with optional `references/` and reusable scenario tests. The KB holds long documentation; the skill routes to it rather than embedding it all.

## Adoption and security checklist for future candidates

1. Inspect commit provenance, license, maintenance, README, code, dependencies and install scripts.
2. Determine: instruction-only `SKILL.md`, developer code sample, MCP server, or full document-editing add-in?
3. Review execution permissions, remote data egress, telemetry, secrets, arbitrary code and Word/Office host availability.
4. Build test cases for intended feature and a **negative safety case**; do not install into active Mac Word without isolation.
5. Reuse a method/abstraction only where it improves current product; do not wholesale replatform to Svelte/AI runtime.
6. Record exact upstream SHA and tested local Word build **if** code is imported; quote/clone only under license.

## Proposed later split if one skill becomes too broad

Current `office-word-addin-engineering` stays the **router**. Only after repeated use shows distinct needs consider tiny focused subskills:
- `office-word-ribbon-ui`: ribbon menu/command design and Mac verification.
- `office-word-document-safety`: mutation/stale-preview/OOXML/Undo guards.
- `office-word-host-diagnostics`: macOS sideload, build provenance, startup and Web Inspector.
- `office-academic-editing`: citations, styles, tracked changes and verification.

**Do not create all four now**: duplicate trigger descriptions and extra skill registry context may make discovery worse. Promote a separate skill only with pressure-test evidence that routing currently fails.

## Source notes

- [Office Developer AI Tools](https://github.com/OfficeDev/Office-Developer-Tools) current README only describes one extended First-run prompt.
- [Office Agents Word README](https://github.com/hewliyang/office-agents/blob/main/packages/word/README.md) describes document read/structure/OOXML, raw Office.js execution and local bridge; parent README marks the product **not production-ready**.
- [Microsoft Skills repository](https://github.com/microsoft/skills) focuses on SDKs/cloud tooling, not dedicated Word add-in troubleshooting.
- [Office Developer code sample ribbon menu manifest](https://github.com/OfficeDev/Office-Add-in-samples/blob/main/Samples/office-add-in-commands/word/manifest.xml).
- [Agent Skills specification](https://agentskills.io/specification).
- [OpenAI Build Skills](https://developers.openai.com/plugins/build/skills).
