# Word DOCX import/append fidelity and persistence

**Use case:** append source DOCX to an existing document with `Body.insertFileFromBase64(..., "End")`. Avoid conflating **import itself** with **false stale-preview preflight**.

**Applicability / last checked (2026-11-10):** Word for Mac, Office.js add-ins. Exact Word/Office build for this case: not recorded in the source evidence; do not generalize to other builds without retesting.
## Operation phases

1. Source selection and identity: file byte length, type, DOCX ZIP signature and bounded size; use disposable fixture.
2. Preview: read target body snapshot only; do **not** mutate. Show destination and potential style/section implications.
3. Preflight and user confirmation: source still selected and same identity; target equivalent to preview under robust fingerprint.
4. **Final** target guard in same Word.run call that inserts the DOCX, then inspect result.
5. Validate source sentinel exactly once, tables, styles, numbering, hyperlinks, fields, section breaks, headers/footers where within declared scope, and Word Undo/Redo. Save/reopen temporary copy for actual persistence.

## Official evidence and examples

- [Word.Body API](https://learn.microsoft.com/en-us/javascript/api/word/word.body?view=word-js): `insertFileFromBase64()`, `getOoxml()`. Check actual Word API support, import options, placement and document size constraints.
- [Microsoft import template sample](https://learn.microsoft.com/en-us/samples/officedev/office-add-in-samples/word-import-template/): shows DOCX import but can replace content. Our target **must append**.
- [Microsoft Get/edit/set OOXML sample](https://learn.microsoft.com/en-us/samples/officedev/office-add-in-samples/word-get-set-edit-ooxml/): supports structural comparisons in a **synthetic** document.
- [Office.js #6703](https://github.com/OfficeDev/office-js/issues/6703): Word for Mac 16.109 regression where import stripped hierarchical numbering indent/style links; Microsoft reports fix deployed June 2026. Thus even a successful import may silently lose document fidelity; verify multi-level lists on the tested host.

## Test matrix

| Fixture feature | Verification |
|---|---|
| Source sentinel `MERGE_SENTINEL_03` | One occurrence, never zero or duplicated on successful Confirm |
| Tables | Row/column counts, border, merged cells, content |
| Styled paragraphs | Style names and formatting preserve |
| Numbered headings | `numId/ilvl` hierarchical indentation preserve |
| Links and fields | Link target remains, fields don't collapse unexpectedly |
| Sections/headers/footer | Expected imported/retained changes per documented API scope |
| Cancel | Target unchanged |
| DOCX source swap before accept | Rejected |
| Target edited before/while dialog | Rejected, including format-only |
| Second click | No second import |
| Undo/Redo | Observed exact history and document state |
| Save/reopen | Same structural result, no corruption |

## Boundaries

- Don't weaken FILE-08 security to get FILE-07 passing.
- Don't assume all styles persist because the sentinel string appears.
- Don't treat `source DOCX size < limit` as confirmation that the import method ran.
- Don't reuse old Word document with unsaved modifications for a fresh merge test.
- Never perform import tests on real user documents.
