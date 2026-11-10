# Office Add-ins platform, manifest and distribution decision guide

**Verified: 2026-11-10. Evidence grade A:** Microsoft Learn primary documentation and Microsoft-maintained sample code. **Portable guidance; not an ETKPlus manifest migration request.**

## Decision in one minute

The installed **ETKPlus × TransTools** Word task-pane add-in is an **Office.js web application with an XML add-in-only manifest**. The manifest identifies the add-in to Word and points to HTTPS-served task-pane resources; the JavaScript executing in the Word webview performs Office.js work. The **unified manifest** is an optional *different packaging/distribution model*, not a solution to stale JS, Add-in Error or FILE-07. Keep the current working XML GUID and port until a separate compatibility/deployment project is approved.

| Goal | Read first | Preferred approach / warning |
|---|---|---|
| Understand what loads where | [Office Add-ins platform overview](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/office-add-ins) | Separate manifest registration, HTTPS server response, webview JavaScript load and Word API initialization |
| Check installed XML task pane, resources, permissions | [Add-in only XML manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/xml-manifest-overview) | Validate `OfficeApp`, `Id`, `Hosts/Document`, `SourceLocation`, `VersionOverrides`, URLs, resource IDs and mandatory XML element order |
| Understand both manifest types | [Office Add-ins manifest overview](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/add-in-manifests) | XML add-in-only vs Microsoft 365 unified JSON are distinct; don't mix elements |
| Compare XML and unified JSON equivalents | [Compare add-in-only and unified manifests](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/json-manifest-overview) | Useful for a **planned** migration, not a Mac startup patch |
| Evaluate unified manifest platform compatibility | [Unified manifest overview](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/unified-manifest-overview) | Mac Word/Excel/PowerPoint **16.103 (25101427)+** support; web and subscribed Windows support; Office mobile and some other platforms not all supported; verify current chart |
| Understand a Microsoft 365 app ZIP package | [App package for Microsoft 365](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/app-package-for-microsoft-365) | ZIP with **manifest.json** plus icon assets and optional files; web app logic is **not** in this ZIP but served via HTTPS. **This package format is for the unified JSON manifest**, not a requirement that existing XML add-ins switch today |
| Support both manifest variants | [Manage unified and add-in-only versions](https://learn.microsoft.com/en-us/office/dev/add-ins/concepts/duplicate-legacy-metaos-add-ins) | Requires deliberate `alternates/hide` coordination; UI may coexist for up to 24 h; don't create duplicate production add-ins casually |
| Verify platform API availability | [Office versions and requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/office-versions-and-requirement-sets), [runtime requirement checks](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/specify-api-requirements-runtime), [Word API requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets) | Test feature-by-feature with `Office.context.requirements.isSetSupported(...)`; a newer Word product version does not guarantee every newer API |
| Initialize safely | [Initialize your Office Add-in](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/initialize-add-in) | Use `Office.onReady()`, verify `Office.HostType.Word`, then bind handlers; don't call Word APIs before Office.js is ready |
| Load the add-in on a Mac | [Mac sideload (XML manifest)](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac) | Installed manifest in Word's `~/Library/Containers/com.microsoft.Word/Data/Documents/wef`; user action in Word Home → Add-ins; distinguish installed manifest from tracked source |
| Debug startup | [Office development troubleshooting](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors), [Mac Safari Web Inspector](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac), [runtime logging](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/runtime-logging) | Confirm host/build, correct JS file, URL, TLS, environment and errors before touching cache |

## Manifest comparison: meaningful distinctions

| Attribute | XML add-in-only | Unified app manifest |
|---|---|---|
| File | `manifest.xml` | `manifest.json` |
| Core identity | `OfficeApp/Id` GUID and manifest `Version` | JSON `id`, `version`, `manifestVersion` |
| Word host | `Hosts/Host Name="Document"`; taskpane ribbon under `VersionOverrides` | `extensions.requirements.scopes=["document"]`, ribbons/runtimes |
| Taskpane page | `SourceLocation`, often resource ID pointing to HTTPS URL | Runtime page URL in JSON |
| Commands | `VersionOverrides` and `ExtensionPoint` | `ribbons`/action runtime declarations |
| Permissions | XML `Permissions`, e.g. `ReadWriteDocument` | JSON `authorization` permissions including resource-scoped formats |
| Distribution | Install/sideload XML via host/admin methods | Microsoft 365 app ZIP containing JSON and required icons |
| Version numbers | Manifest product version; *not the JS build identity* | Manifest product version; *not the JS build identity* |

### Two especially easy-to-miss facts

1. The XML manifest documentation specifies **strict element order**, not only valid element names. A seemingly sensible reordering can make sideload invalid. Validate the exact installed XML and the source XML; do not change the GUID as a debugging shortcut.
2. The Microsoft 365 unified app package guide's icon requirements (for example `color.png` and `outline.png`) belong to that JSON app package. **Do not misapply them to our existing XML add-in**. For XML, validate the `IconUrl`, `HighResolutionIconUrl` and `bt:Image` resources appropriate to its manifest schema.

## Minimal initialization diagnostic contract

Microsoft's [Word Hello World](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world/word-hello-world) demonstrates the irreducible stack:

```text
installed manifest → HTTPS taskpane.html → Office.js CDN → Office.onReady(Word host)
→ button click → Word.run → context.sync → document mutation
```

During **non-mutating** startup investigation, stop at the Office.onReady and button-binding stages. A working web server and `curl 200` prove only server reachability. A rendered page without a successful Word API call doesn't prove the Office bridge works. A build label loaded from an independent JS metadata file does not prove the executing application module matches it.

Use [the separate canary runbook](../troubleshooting/OFFICIAL-HELLO-WORLD-CANARY.md) for controlled A/B Word runtime diagnosis.

## ETKPlus-specific decision (2026-11-10)

- Retain the XML add-in-only manifest, GUID, HTTPS `localhost:3001`, and product version unless a deliberate product or distribution decision requires change.
- Separate installed manifest consistency, taskpane HTML/JS responses, Office.js CDN network load, `Office.onReady`, build identity lock, and Word API readiness.
- Compare the last user-observed working `.005` code with `.007` (which also loaded during an earlier session, but failed with Add-in Error on a later smoke run) under identical host conditions. Treat **intermittent host error** as a competing hypothesis rather than assuming a deterministic code regression.
- Do not introduce unified JSON, Microsoft 365 Agents Toolkit, admin deployment or auto-run event handlers as an unexplained repair for the existing XML app.
- No tested Word add-in, static sample manifest or deployment configuration was changed as part of documenting these sources.

## Related official follow-up links

- [Word add-in tutorial](https://learn.microsoft.com/en-us/office/dev/add-ins/tutorials/word-tutorial): text/range/paragraph/image/table/content control examples; queue work under Word.run, synchronize deliberately.
- [Mac Word debug](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/debug-office-add-ins-on-ipad-and-mac): Safari Inspector requires Office installed from Office.com rather than App Store according to Microsoft's current article; `defaults write ... OfficeWebAddinDeveloperExtras -bool true` alone does **not** prove Inspector is available.
- [Word events](https://learn.microsoft.com/en-us/office/dev/add-ins/word/word-add-ins-events): event lifecycle and co-authoring; requires runtime capability checks; no reason to make events the only authorization for a stale preview.
- [Microsoft Learn word docs hub](https://learn.microsoft.com/en-us/office/dev/add-ins/word/): overview, quickstart, development, test and publish entry points.
- [Sideload add-ins with unified manifest](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-add-in-with-unified-manifest): only when actively evaluating a **new isolated** unified-manifest variant.

**Maintenance:** recheck the live compatibility table before deploying a new manifest type; a support entry is not evidence that the user's installed Word can load our configuration. Last verified: 2026-11-10.
