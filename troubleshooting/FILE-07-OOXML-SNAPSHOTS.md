# FILE-07 — False stale-DOCX preview rejection caused by nondeterministic snapshot representation?

**Status (2026-11-10): OPEN / root cause unproven.** Diagnostic evidence supports nondeterministic **OOXML serialization** of an unchanged target, but `w:rsidR` has not been proven the only variation. Never turn this hypothesis into an unconditional allowlist.

**Applicability / last checked (2026-11-10):** Word for Mac, Office.js add-ins. Exact Word/Office build for this case: not recorded in the source evidence; do not generalize to other builds without retesting.
## Symptom and exact code path

A clean Word document is opened; source DOCX preview succeeds, then pressing **Append** raises "Belge önizlemeden sonra değişti; yeniden tara." **before** confirmation or any inserted content. The app calls `inspectAppendDestination()` in preview and again in `confirmAndRun` preflight; it also repeats the guard in `appendDocxAtEnd()` within the same `Word.run` as mutation. Current guard is byte-for-byte equality of `body.text` and `body.getOoxml()`.

**Measured 2026-11-10 Word Mac evidence on a synthetic clean-control target:** three 208-character text snapshots and hashes were identical; three OOXML strings each had 374234 characters, but all three SHA-256 values differed. The first structured difference showed `w:rsidR`. Another run had different XML length and child counts. UI AX text truncated remaining diff, therefore no complete difference set is established. FILE-08 separately proved a real edit triggers correct rejection.

## Related upstream facts

1. [OfficeDev/office-js #4078](https://github.com/OfficeDev/office-js/issues/4078): 2024 report of nondeterministic `w14:paraId` in Mac Word. Microsoft said it was fixed in July 2026. **Different attribute**, not proof that `w:rsidR` remains affected.
2. [Microsoft OOXML guidance](https://learn.microsoft.com/en-us/office/dev/add-ins/word/create-better-add-ins-for-word-with-office-open-xml): `w:rsid*` denote revision/edit-session identifiers used in document comparison/combine. It describes how to omit them in **markup being generated**. That is not blanket permission to strip them from a live document's security fingerprint.
3. [Word.Body API](https://learn.microsoft.com/en-us/javascript/api/word/word.body?view=word-js): `getOoxml()`, `insertFileFromBase64()`. Consult required Word API set.

## Controlled, read-only diagnosis

1. Verify task pane **runtime build ID + Git SHA** (not just hardcoded v1.2) and exact Word host/build. Check source copy SHA/ZIP; use one disposable synthetic target only.
2. Capture snapshots A, B immediately consecutive, and C after normal preview-to-preflight UI transition, *without mutating the document*. Count entire `text` equality and OOXML equality; record string lengths/digests.
3. To avoid AX text clipping, create a **local JSON diagnostic artifact**, containing only element/attribute paths, class/count of differences, canonical categories, and hashes. Keep full XML in temporary memory only; never commit or upload full source text, OOXML or personal metadata. Bound diagnostic size, handle malformed XML and namespaces.
4. Parse XML with a safe XML parser (DTD/external entity/network disabled). Normalize only XML representational trivia in a **measurement experiment**: attribute order, namespace prefix variations. Classify but do not remove semantic attribute differences.
5. Group all observed changes: `w:rsidR`, `w:rsidRPr`, `w:rsidP`, `w:rsidDel`, `w14:paraId`, `w14:textId`, relationship IDs, document settings, fields, body/table/formatting, part order. Verify document text remained equal.
6. Repeat on a freshly opened QA copy and (if feasible) a second Word session. In parallel, capture deliberate **format-only**, table, field/content-control, image and text mutations to test fingerprint sensitivity.
7. Only after complete evidence: propose a narrowly scoped equivalence rule for *proved* volatile attributes, with hard tests that all meaningful document changes still trigger rejection.

## Safe implementation invariants

- Never use **text-only** security checks.
- Never remove the final stale guard in the **same Word.run batch** as insertion.
- Never call the mutator directly from DevTools to bypass user approval.
- Never turn a failed/unknown XML parse into "equal"; **fail closed**.
- Never log user document contents or full OOXML into GitHub.
- Snapshot validity covers the **document body** unless expanded explicitly; do not advertise whole-document/header/footer guarantees.

## Regression suite

| Case | Expected |
|---|---|
| Unmodified A, B, C snapshots | Equivalent; able to proceed to confirmation |
| Intentional edit after preview (FILE-08) | Explicit rejection, zero insertion |
| Format-only edit | Explicit rejection |
| Table/field/content-control/image/relationship change | Explicit rejection where within guarded scope |
| Source file changed during preview | Explicit rejection |
| Cancel | Zero mutation |
| Confirm once | Exactly one `MERGE_SENTINEL_03`, source table preserved |
| Double-click/Enter | No second append |
| Save/reopen temporary copy | Original/inserted structure and styles remain intact |

**Release gate:** FILE-07 is currently FAIL; FILE-08 stale rejection PASS, early-button invalidation FAIL; remain independent.

## Evidence links

- [PR #5](https://github.com/Centaurioun/ETKPlus-TransTools/pull/5)
- [FILE-07 snapshot observations](https://github.com/Centaurioun/ETKPlus-TransTools/blob/58b0862961a69f38a2de3a1fc8fd538c470f9ae8/docs/qa/results/evidence/2026-11-10-file07-snapshot-diagnostic/observations.md)
- [PR #5 engineering note](https://github.com/Centaurioun/ETKPlus-TransTools/pull/5#issuecomment-6753375928)
