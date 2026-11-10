# Microsoft OfficeDev Word samples — annotated engineering atlas

**Verified: 2026-11-10.** Official public repository: https://github.com/OfficeDev/Office-Add-in-samples . Focused inspection included actual README, `manifest-localhost.xml`, `taskpane.html`, and Word sample directory structure. Prefer small isolated experiments and **link to vendor code**, not copy-paste entire vendor repositories.

## Which sample to use for which engineering task?

| Official OfficeDev sample | Actual demonstrated behavior | Recommended use in a Word project | Limit |
|---|---|---|---|
| [Word Hello World](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world/word-hello-world) | Minimal taskpane, XML + unified variants, `Office.onReady`, single button + `Word.run` insert paragraph | **P0 canary** for Add-in Error, server/manifest/Office bridge distinction; compare bootstrap stages | Minimal sample is **not** a proof of complex ETK functions or production security |
| [Hello World family](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/hello-world) | Comparable Word/Excel/PowerPoint/Outlook minimal add-ins | Compare shared Office model and host-specific differences | Do not try Excel/Outlook manifest inside Word |
| [Word add-in commands](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/word) | Home ribbon command, task pane button, dropdown, separate `FunctionFile` command JS | Ribbon button missing/disabled, command handlers not firing, resource URLs | Taskpane and `ExecuteFunction` lifecycles differ |
| [Auto-open task pane with document](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/office-add-in-commands/auto-open-task-pane) | Enables a specific saved document to reopen an add-in pane; AddInCommands 1.1 | Evaluate document-specific convenience after functional baseline | **Not** a fix for developer add-in disappearing globally after Word restart; README says not supported for Marketplace distribution |
| [Completed Word tutorial](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/tutorials/word-tutorial) | Word taskpane with range/paragraph/text, HTML, images, tables, content controls | UI smoke tests, range-selection/format tests, Word.run interactions | Real Word usage and API requirement sets still required |
| [Import Word document as template](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-import-template) | User selects a DOCX and imports it into active document with Office.js | Check `insertFileFromBase64` API and file-picker UX for FILE-07, source validation | Sample **replaces** content as template, while ETK **appends**; it does not implement our stale-preview fingerprint |
| [Manage citations](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-citation-management) | Select `.bib` file; parse BibTeX via `@orcid/bibtexParseJs`; insert citation as Word endnote with `insertEndnote` | **Academic/periodontology extension** design: source ingestion, citation picker, exact selected range, footnote/endnote API | NOT a full Zotero, CSL, cross-reference, bibliography or citation validation engine; bibliography fidelity separate |
| [Get/edit/set Office Open XML](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-add-in-get-set-edit-openxml) | Read selected content as OOXML, edit/roundtrip content | Reproduce XML serialization issues with small synthetic document segments | Legacy Visual Studio/C# assets; don't transplant runtime or whole project |
| [Load/write Office Open XML](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-add-in-load-and-write-open-xml) | Rich OOXML sample content: formatted text, table, image, drawing, content control, chart, SmartArt | Fixture design for table/image/content-control style fidelity; compare OOXML structure | README prefers typed Word APIs where available; older Visual Studio sample |
| [Automatically label document on open](https://github.com/OfficeDev/Office-Add-in-samples/tree/main/Samples/word-add-label-on-open) | `OnDocumentOpened` event handler changes header text on open | A **separate future admin-deployment** use case | **Not** a Mac sideload canary: sample applicability is Word Windows/web, and README states event activation requires administrator central deployment (not sideload or Marketplace) |

## High-value source inspections

### Word Hello World: exact observations

Files:
- [README.md](https://github.com/OfficeDev/Office-Add-in-samples/blob/main/Samples/hello-world/word-hello-world/README.md)
- [manifest-localhost.xml](https://github.com/OfficeDev/Office-Add-in-samples/blob/main/Samples/hello-world/word-hello-world/manifest-localhost.xml)
- [taskpane.html](https://github.com/OfficeDev/Office-Add-in-samples/blob/main/Samples/hello-world/word-hello-world/taskpane.html)
- [manifest-configurations/unified/manifest.json](https://github.com/OfficeDev/Office-Add-in-samples/blob/main/Samples/hello-world/word-hello-world/manifest-configurations/unified/manifest.json)

The example's XML is an `OfficeApp` with `xsi:type=TaskPaneApp`, `Host Name=Document`, `SourceLocation` and taskpane ribbon `VersionOverrides`. Its sample localhost port is **3000**, while ETK uses **3001**. These are *different* sample/project settings; never copy sample manifest wholesale into a working add-in because GUID, SourceLocation, ribbon IDs, icons and resource references differ.

The HTML loads `office.js` from the Microsoft CDN, waits for `Office.onReady`, checks Word host, then binds the button; click calls `Word.run`, queues `body.insertParagraph`, and sends via `context.sync`. This lets us split failures into: webview/HTTPS, Office.js CDN, host-ready, click wiring and Word API bridge.

**Privacy/security warning**: the official sample's `taskpane.html` contains an external image request to a `pnptelemetry.azurewebsites.net` address described in-source as sample telemetry. Remove this optional external tracking element from any **locally copied test page**, and do not reuse vendor sample in client environments with confidential Word documents. For canary tests, use a blank synthetic document.

**Isolation warning**: do not sideload sample using ETK's manifest GUID, current `wef` file, port 3001, or owned runtime process. Choose a distinct unique GUID, distinct localhost port, distinct taskpane entry and isolated certificate trust. Only the main Codex controller may touch Word UI or start/stop the test server, and only after identifying process ownership. Record exactly which sample/code commit ran.

### Word ribbon commands

In `Samples/office-add-in-commands/word`, the XML manifest defines buttons and dropdowns under `ExtensionPoint`, with a `FunctionFile` (HTML) that maps `ExecuteFunction` actions to command JS functions. This can separate a missing Word ribbon command from a failing taskpane webview. It is **not sufficient** to show that taskpane code initialized. Read the official [create add-in commands guide](https://learn.microsoft.com/en-us/office/dev/add-ins/develop/create-addin-commands) when adopting command architecture.

### Auto-open is a *document behavior*, not a sideload repair

The `Samples/office-add-in-commands/auto-open-task-pane` README describes configuring a saved document so its taskpane opens on next document open. It requires the **AddInCommands 1.1** requirement set and, according to the sample, supports centrally deployed or sideloaded add-ins, **not Marketplace**. It cannot repair a disabled or missing Developer Add-in in Word's global add-in store, and must not be used to force-open all user documents. Investigate only after the normal startup/ribbon pathway is reliable.

### Citation management: specialized opportunity

The sample accepts a **BibTeX .bib** input, parses it with a named third-party JS library, displays a reference picker, then uses `Range.insertEndnote` to add selected citation info as an endnote. Potential future academic use: enable read-only imported bibliography inspection and explicit citation insertion for manuscripts; preserve Turkish/English citation styles separately. **It is not a Zotero connector** and not a full reference formatting system. Require source attribution, reference ID stability, and document-safe insertion before proposing adoption.

### OOXML samples: choose typed Word APIs first

The `word-add-in-load-and-write-open-xml` README explicitly recommends current Word API requirement-set methods when available and OOXML fallback otherwise. Rich markup samples can help build synthetic fixture tests for formatting/table/image fidelity, but the older project scaffolding is not the recommended runtime for a modern Mac Word taskpane. For snapshot security see [FILE-07 runbook](../troubleshooting/FILE-07-OOXML-SNAPSHOTS.md), with native host-level evidence and fail-closed guards.

## Priority map for ETKPlus × TransTools

| ETKPlus symptom | Best references | Next safe action |
|---|---|---|
| Word panel Add-in Error, 45 smoke cases blocked | Word Hello World; Mac sideload guide; initialization guide | Isolated canary with identical Word host but own GUID/port; compare stage to `.005` and `.007` |
| Ribbon tile missing/disabled after restart | Word commands; XML manifest docs; upstream Office.js #6973 | Verify installed XML, command URLs and host-side persistence without assuming canary equals ETK |
| DOCX import append status | Import template; Word.Body API; OOXML samples | Don't restart deep FILE-07 investigation while full smoke survey blocked; keep historical `.005` user-confirmed insert separate from current build |
| TXT-04 selected range and formatting | Completed Word tutorial; Word.Range API | Test scoped replacement on synthetic fixture; compare selected ranges |
| TABLE / IMG tests | Completed Word tutorial; OOXML rich content sample | One clean fixture per mutating operation; verify visible result and style |
| Academic citations | Citation management sample | Optional **later** feature exploration; do not add external services or new imports during P0 |
| Automatic add-in opening | Auto-open per-document sample; document-on-open label sample | **Defer**; neither is a generic Mac sideload-persistence fix |

## Review and license policy

The source repository supplies its own LICENSE and individual files may have attribution statements. This knowledge base preserves **links and original engineering commentary**, not copies of third-party code. Before copying a code snippet into a product, read its applicable license and any sample-specific instructions. Always inspect/disable incidental telemetry and networking. Archive neither private Word content nor raw OOXML from clients.

**Latest review:** 2026-11-10. Recheck upstream sample versions before use; sample projects may have older scaffolding and CDN URLs even if the parent repository is active.
