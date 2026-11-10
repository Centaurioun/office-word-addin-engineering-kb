# Microsoft 365 Agents Toolkit — where it helps Office Word add-in engineering

**Source review 2026-11-10.** This corrects an overly broad earlier dismissal of Agents Toolkit as only relevant to Teams/Copilot agents. **No Toolkit run, installation, or Mac Word project generation was performed in this KB update.**

## Supported and unsupported conclusions

Microsoft documents creating **Office Add-in** projects in Microsoft 365 Agents Toolkit, including Word/Excel/PowerPoint. [Official guide](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/agents-toolkit-overview) states:
- Choose **Create a New App → Office Add-in → Task pane** in Agents Toolkit.
- Word/Excel/PowerPoint creation uses the Toolkit **prerelease** VS Code extension and creates **unified JSON manifest** projects; the manifest support is described as **preview** for those hosts.
- The official illustrated Word/Excel/PowerPoint **F5 desktop sideload instructions apply to Windows**. Do not assume the same debugger and WebView behave the same on Mac.
- The example scaffold can target multiple Office host apps via unified manifest; host feature availability and permissions still differ.

[Official toolkit Hello World Office Add-in sample](https://github.com/OfficeDev/microsoft-365-agents-toolkit-samples/tree/main/hello-world-office-addin) includes `m365agents.yml`, `appPackage/manifest.json`, VS Code config, taskpane source, webpack build and manifest validation guidance. It demonstrates **unified JSON distribution across Word/Excel/PowerPoint/Outlook**, *not* a drop-in patch for ETKPlus's XML manifest. The sample's README focuses on Excel/Windows-based debugging.

## What ETKPlus can reuse without migrating manifest

| Need | How Toolkit helps | Boundary |
|---|---|---|
| Ribbon+taskpane architecture | Compare taskpane and command wiring in a current Microsoft-generated project, and [OfficeDev Word XML command-menu sample](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word) | For actual ETK XML menu implementation, Word XML example is **closer**; Toolkit sample is primarily unified JSON |
| Project structure | Review folder roles, build and deployment scripts, assets and resource identifiers | Do not replace tested `src/core` and `src/office` modules |
| Feature testing | Isolated sample canary on non-colliding port and GUID | A sample that opens does not prove current ETK .007 works or supports all features |
| Validation | Toolkit's `Microsoft 365 Agents: Validate Application` / `atk validate` for **Toolkit projects and JSON** | Keep `office-addin-manifest` or Microsoft XML tools for ETK XML; don't claim `atk validate` validates existing XML |
| Skill use via VS Code | Copilot harness uses project `.github/skills`; Codex harness uses `.agents/skills` (VS Code experimental integration may also discover `.github/skills`) | Loading/discovery and behavior are separate tests; identical files can be mirrored but do not overwrite an independently modified version |
| Future JSON migration | Prototype separately with real Word Mac/Windows and delivery channels tested | **Not a prerequisite** for ribbon, taskpane or task functionality |

## Current two-harness handoff, reported 2026-11-10

- Copilot-integrated Codex was running a **local MVP checkout** where `mac-word-mvp/public/taskpane.html` has **4 buttons**; the P0 bugfix worktree has **60 button elements**. This explains incomplete inventory. Do not silently claim they refer to the same code revision.
- In Copilot Codex, user reported two mirrored skill locations `.agents/skills/office-word-addin-engineering` and `.github/skills/office-word-addin-engineering`, both updated with Toolkit routing. Reported local Word skill blob `4a8a3eb21c11502d36dd4366627bea7ca13dfcc5` and router blob `59b912da1eebfd3d8548391066374e86be3aef03`; **these local bytes have not been fetched directly by this KB author**.
- Central KB still has previous skill blob `b597395f0aef92787e798c8b8a5bd8ae86900bf0` as of this review. **Before syncing**, compare local both copies to KB `main`, preserve diff/history, check user changes and commit one canonical revision to KB; then update mirrors from reviewed source. Do not automatically overwrite.
- Copilot's skill discovery through slash menu remains unverified in local agent report; native Codex's catalog loaded skill. External validator was unavailable; 10 independent behavior pressure scenarios were not executed.
- GitHub [Issue #7](https://github.com/Centaurioun/ETKPlus-TransTools/issues/7) is a design backlog. Separate [Issue #6](https://github.com/Centaurioun/ETKPlus-TransTools/issues/6) now has 30 COMPLETE / 1 FAIL / 5 BLOCKED / 9 NOT_RUN after a native Word .007 survey. Do not restart a 0/45 startup project based on earlier status.

## Reuse approach and explicit decision

**Do:** use Agents Toolkit as an isolated Microsoft example/scaffolding/JSON validation capability, routed from the Word engineering skill *to the Toolkit skill* if available; use XML Word ribbon example for actual planned XML ribbon. Study but do not import code blindly; record host support, sample commit, license and user privacy.

**Do not:** install new prerelease packages, convert ETK XML to unified JSON, change manifest GUID, terminate unrelated servers, or copy third-party telemetry on a running product without separate approval. No direct Office.js AI execution on confidential Word manuscripts without a careful security design.

## Reading

- [Microsoft official Agents Toolkit Office add-in project creation](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/agents-toolkit-overview)
- [Official Toolkit Hello World Office Add-in sample](https://github.com/OfficeDev/microsoft-365-agents-toolkit-samples/tree/main/hello-world-office-addin)
- [Microsoft OfficeDev Word XML ribbon command example](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word)
- [VS Code skill locations and cross-harness discovery](https://code.visualstudio.com/docs/agent-customization/agent-skills)
