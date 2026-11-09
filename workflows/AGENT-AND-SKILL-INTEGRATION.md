# Reusing Office.js knowledge in Codex, OfficeDev and future AI agents

## Existing authored skill (provenance)

Maintained in this repository:

- [SKILL.md](../.agents/skills/office-word-addin-engineering/SKILL.md) — Office.js Mac Word diagnosis, safe Word.run edits, manifest/sideload, Word QA, stale previews.
- [agents/openai.yaml](../.agents/skills/office-word-addin-engineering/agents/openai.yaml) — display metadata.

The skill links to this repository’s portable workflows. Keep ETKPlus-specific branch, fixture and Mac UI details in [the ETKPlus case guide](../cases/ETKPlus/README.md), not in the generic skill.

## Recommended agent workflow

1. Identify failure class: startup, native API, OOXML, selection/range, unsafe preview, dialogs, Undo/Redo, import structure.
2. Read exact matching troubleshooting page and 2–3 official references.
3. Confirm runtime build ID + supported WordApi set before changing code.
4. Apply **superpowers/systematic-debugging + test-driven-development** where available: reproduce, explain, add failing test, minimal fix, verify, report.
5. Use Microsoft Learn MCP when **actual search/fetch functions are available**. `codex mcp list/get` proves configuration only; run real documentation search to prove service operational. Endpoint `https://learn.microsoft.com/api/mcp`.
6. Use `office-addin-manifest` as transient validator. Don't blindly install `office-addin-debugging`: it can alter registered sideload/server. `office-addin-mock` optional and cannot prove native Word behavior. Test-server/helpers only if a structured harness is planned.
7. Preserve current running add-in, manifest GUID, HTTPS port, certs, other add-ins and user's documents. No direct unsafe console mutation.

## Microsoft Office Developer AI Tools

- [OfficeDev/Office-Developer-Tools](https://github.com/OfficeDev/Office-Developer-Tools): planned/prompts/skills/agents/scripts; on 2026-11-10 README documents First-run Precheck and an unrelated Outlook pin script. **Do not claim a mature Word Codex skill is currently included**. Watch releases and evaluate new tools individually.
- [Official prompt library](https://learn.microsoft.com/en-us/office/dev/add-ins/resources/resources-github-copilot-prompt-library): examples including Word insertFileFromBase64. These are Copilot-targeted **prompt examples**, not tested end-to-end ETK fixes.
- [OfficeDev/Office-Addin-Scripts](https://github.com/OfficeDev/Office-Addin-Scripts): npm tooling library, not skill registry.

## Potential later AI Word editor architecture (not current P0 scope)

[Microsoft Community Learning: Move Over, Copilot — Building Your Own AI in Office](https://www.youtube.com/watch?v=jbCfs7VQY3A) illustrates:
- `AIContextResolver`: selected vs whole-document context;
- `ToolRegistry` and Strategy Pattern: independent actions, descriptions, schema and execution;
- `Office.js` operations: comments, insert, formatting, highlight;
- Word paragraph events: reactive co-editor.

These are architectural ideas, **not permission for autonomous document edits**. Before adding LLM calls, require an explicit data egress consent model, private-document policy, human review of proposed edits, and least-privilege tool schemas. Keep human confirmation and stale target guard. Do not send thesis, manuscript, patient or confidential data to an external AI service by default.

## Skill discovery validation

- YAML/frontmatter syntax parse PASS does not prove Codex loads the skill.
- Start a fresh Codex session **from the intended project/workspace**, list discovered skills, explicitly invoke `office-word-addin-engineering` on a benign read-only question and inspect actual usage.
- If a runtime omits installed skill tools or Learn MCP functions, record BLOCKED with exact platform/version and proceed to official web docs; don't claim integration verified.
- Keep general skill in central repo; projects may pull a **versioned copy** or vendor it with documented upstream hash. Avoid silent untracked duplication.
