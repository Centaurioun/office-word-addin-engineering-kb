# Reliable Mac Word QA / evidence protocol

Goal: reproducible *real Word* evidence without modifying a user's documents or overclaiming from mocks. Applicable to any Word Office.js add-in.

## Before running

1. Verify actual project worktree, HEAD SHA, uncommitted changes, compiled JS build identity, served URL/port and Word/macOS/Office.js API requirement set.
2. Verify exact fixture **SHA-256** and ZIP integrity; clone a new disposable fixture; retain original unmodified.
3. Ensure only **one active QA document** is open. Don't close documents you cannot positively identify as QA-owned.
4. Verify the Word task pane's **JS-embedded runtime build ID**, not merely HTTP 200, source file timestamp or static v1.2 HTML label.
5. Log named action, expected document scope, user-approved mutation, test capability/limitations and a safe rollback.

## Evidence row schema

`test_id, outcome, host_name, os_version, word_build, api_set, manifest_guid, taskpane_url, app_semver, dev_build, git_sha, dirty_fingerprint, asset_digest, fixture_sha256, precondition, action, result_observed, document_integrity, screenshot_or_log, evidence_limitations, reviewer, utc_timestamp`.

Never claim more than was observed. Textual accessibility evidence is acceptable but clearly label it **AX observation, not screenshot**. Avoid leaking complete user content/OOXML or credentials.

## Results

- **PASS:** test's full stated acceptance criteria observed on the exact tested build and fixture; include negative safety checks where relevant.
- **FAIL:** clear mismatch in real Word, supported by observation.
- **BLOCKED:** host/environment/interaction prevents reaching behavior, e.g. task pane didn't load; not necessarily product fault.
- **NOT_RUN:** not attempted.
- **NOT_SUPPORTED:** verified host/API requirement mismatch; link official API contract.

Use **dimension-level** rows for compound scenarios. Example FILE-08 apply-time rejection PASS but early UI invalidation FAIL; do not average to PASS. Keep historical conflicts (e.g. TXT-16 Redo) with separate build/fixture/run IDs, don't rewrite old evidence.

## Safe test sequence

- Preview no mutation; separate Cancel copy and Confirm copy.
- Confirm requires actual in-pane dialog, explicit user action; check double-submit and no silent mutation.
- After mutating: inspect exact changed text, **bold/italic/styles**, tables/fields/images/hyperlinks and unchanged regions. Don't infer "format preserved" from unchanged word count.
- Undo/Redo using Word controls: count history units; test when the document rather than taskpane is focused; capture each state; don't claim atomicity if Word doesn't offer it.
- FILE import must verify source sentinel exactly once, source table, source link/numbering/style and persistence on a separately saved disposable copy.
- Close QA copy via Word UI and choose **Don't Save** after collecting evidence. Never bulk-close all Word documents; if uncertain, stop.
- Return to queued independent cases after BLOCKED; don't repeatedly click an inert control.

## Automated tests are supportive only

`npm ci`, `npm run typecheck`, `npm test`, `npm run build`, manifest validation and mocked Word APIs do not establish real-Word behavior. Include exact counts and test names; record whether dev server was restarted/reloaded with new JS. If product code didn't change, don't claim a fix because tests remain green.

## Evidence escalation

For a mysterious host behavior, build a **read-only diagnostic** with exact runtime version. Gather count/length/hash/structural diff, then remove instrument code or commit a controlled debug toggle. If access is blocked, record the blocker and avoid inventing measurements.

Private data policy: only synthetic QA documents, redacted structural metadata; no patient info, unpublished thesis text, authentication tokens or raw DOCX/OOXML. Optional screenshot should crop out personal data.
