# Stale previews, explicit confirmation, async safety (FILE-08 / PRO-02 / TXT-16)

**Problem model:** read-only preview and a later document mutation are separate operations. User, Word, another add-in, or coauthor can change the target in between (time-of-check/time-of-use, TOCTOU).

**Applicability / last checked (2026-11-10):** Word for Mac, Office.js add-ins. Exact Word/Office build for this case: not recorded in the source evidence; do not generalize to other builds without retesting.
## Two independent controls

1. **Early UX invalidation**: if a supported Word change event is delivered, clear pending preview and disable Apply, showing "Belge değişti — yeniden tara." **Failure makes UI misleading**.
2. **Final apply-time guard**: compare a robust target fingerprint inside the same Word.run as mutation. This must work even if the task pane was inactive, events were missed or events unavailable. **Failure can permit unsafe mutation**.

**FILE-08 observed 2026-11-10:** app-time stale rejection PASS (sentinel typed after preview and visible error; source wasn't appended), but button remained enabled — early invalidation FAIL. Treat as separate acceptance checks.

## Lifecycle with strict user authorization

`preview -> immutable pending snapshot -> click -> preflight with visible status -> accessible Cancel/Confirm -> final guard -> Word mutation once -> cleanup + audit status`.

- Explicit confirmation remains mandatory for destructive/high-impact operations. Don't use unsupported `window.confirm()` / `window.prompt()` in an Office task pane; use accessible in-pane dialog controls.
- A single flow lock prevents double-click/Enter duplicate submissions. Reset it on **all** cancel/error/success paths.
- On Cancel: never mutate, never persist new document content.
- On missing/unsupported API, stale preview, invalid source file or unknown parsing error: **fail closed**.
- Keep snapshots short-lived; invalidation after known add-in mutations is mandatory; host-driven invalidation is best effort only.
- Document identity matters: attaching the same snapshot to a different open document must be prohibited.
- Do not assert that a **body-only** snapshot covers header/footer or every comment/metadata zone.

## What listeners to investigate

- [Word events](https://learn.microsoft.com/en-us/office/dev/add-ins/word/word-add-ins-events) documents `Document.onParagraphAdded`, `onParagraphChanged`, `onParagraphDeleted`, and content control events. Check each API requirement set **on the active Word host** before attaching; earlier project host reported WordApi 1.4, and paragraph event features may require newer sets.
- Events can be triggered by manual edits, add-in edits and coauthors. Handlers are tied to task pane lifecycle; they are **not persistent durable observers**. Do not use these events as final security authorization.
- Use bounded invalidation, no repeated expensive full XML serialization on every keystroke. Avoid event-induced feedback loops.

## Mock + actual Word acceptance

| Case | Expected |
|---|---|
| Preview read-only | No document mutation; Apply enabled only when eligible |
| Cancel | Zero mutation and clear status |
| Valid Confirm | One Word mutation, correct target |
| Double-click / Enter spam | At most one mutation |
| Manual text edit after preview | Immediate invalidation if supported; **always** final rejection |
| Format-only/table/image changes | Final guard rejects changes in its declared scope |
| Change during confirmation dialog | Final guard rejects even if preflight passed |
| App changes document | Clear every dependent preview |
| Unsupported Word event | UI may not update early, final guard still protects |
| Focus/Tab/Escape | Task pane dialog remains keyboard reachable and returns focus |
| Different document activation | Snapshot must not be reusable |

## Risks to avoid

- Never reduce OOXML guard to text equality simply to make FILE-07 pass. Its nondeterminism requires a **narrow, evidence-backed equivalence algorithm**.
- Never bypass human confirmation to exercise `appendDocxAtEnd`.
- Never claim that "no mutation" proves stale rejection when a click handler did not run.
- Never conclude all confirmation flows pass from a successful PRO-02 test; test FILE, TABLE, IMG separately.

## Sources
- [Word event triggers and lifecycle](https://learn.microsoft.com/en-us/office/dev/add-ins/word/word-add-ins-events)
- [Word API requirement sets](https://learn.microsoft.com/en-us/office/dev/add-ins/reference/requirement-sets/word-api-requirement-sets)
- [Microsoft testing](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/test-debug-office-add-ins)
