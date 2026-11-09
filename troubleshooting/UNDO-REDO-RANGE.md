# Word Range, formatting, Undo and Redo (TXT-04 / TXT-16)

**Project facts (2026-11-10):** TXT-16 removed 7 redundant tabs, Cancel preserved source, Word Undo restored tabs; Redo was disabled ("Can't Repeat") in latest run, though an older report claimed Redo succeeded. TXT-04 on a stress fixture reported 12/12 punctuation-space fixes in latest run vs historical 12/11; full formatting and redo validation incomplete. Keep conflicting records.

**Applicability / last checked (2026-11-10):** Word for Mac, Office.js add-ins. Exact Word/Office build for this case: not recorded in the source evidence; do not generalize to other builds without retesting.
## Upstream facts (not guaranteed identical to our host)

- [OfficeDev/office-js #2225](https://github.com/OfficeDev/office-js/issues/2225) reports `Word.Range.expandTo()` unexpectedly adding Undo-history units. Case originally Word on the web; closed as not planned. Useful **test hypothesis** for extra history units.
- [OfficeDev/office-js #1827](https://github.com/OfficeDev/office-js/issues/1827) requests a Word Office.js `UndoRecord` equivalent. Do not promise VSTO-level atomic Undo from Office.js without official support.
- [Stack Overflow discussion](https://stackoverflow.com/questions/66388993/office-word-add-in-js-how-to-avoid-actions-taken-by-the-add-in-to-appear-in-th) describes separate history units per add-in action; lower-authority community evidence.
- [Word.Range API](https://learn.microsoft.com/en-us/javascript/api/word/word.range?view=office-js): search/getRange/insertText semantics. Read the exact selection and apply location supported by the tested host.

## Diagnostic procedure

1. Use fixture with multiple formatting runs: bold, italic, URL `https://example.org/a.b`, decimal `3.14`, terminal punctuation and partial selection.
2. Save read-only **before** structural/format fingerprint and **visible** selected range boundaries. No full-doc text rewrite to fix one space.
3. Preview; log counted candidates and precise search matches. Compare count to *actual Word Range edits*, not raw regex candidates alone.
4. Apply once on **disposable copy**, check exact target and untouched content/format. Record whether selection remained stable.
5. Word Undo with **Word toolbar/menu focused**, count each action, record text/format after each step. Do not mistake Ctrl/Cmd+Z inside task pane for a Word document undo.
6. After reaching original target state, inspect whether Word Redo is enabled; redo the same number of steps and check final state. If Redo is disabled, preserve it as explicit host/UI observation and test independently whether the add-in performed a hidden post-Undo document change.
7. Repeat with each mutating command in isolation. Record per-operation Undo units; **multiple units alone is not a failure** unless specified in acceptance contract.
8. Use reduced test cases to isolate `Range.expandTo`, `Range.search`, `insertText`, and programmatic selection; don't assume every method has equivalent stack behavior.

## TXT-04 regression design

- Case A: a punctuation match entirely in the selected range interior.
- Case B: terminal match at range boundary.
- Case C: select through final punctuation vs stop before punctuation.
- Case D: last space inside table cell; skip or mutate explicitly per contract.
- Case E: bold/italic mixed runs; preserve formatting.
- Case F: decimal/URL unchanged.
- Case G: multiple sequential matches, count preview vs changed.
- Case H: Undo sequence and Redo state.

**Pass criteria:** exact intended text results, no collateral formatting/style changes, and a documented reversible history behavior. A report "12 ranges changed" without inspecting formatting/undo is only partial.

## Safety

- Don't repeatedly invoke Undo/Redo via stale accessibility IDs; verify current toolbar/menu state to avoid accidental Print dialogs.
- Don't convert `Cant Repeat` into a blanket Office.js failure without isolating focus/history.
- Don't create ad-hoc "Undo" that restores whole OOXML until it is explicitly designed and separately validated.
