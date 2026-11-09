# ETKPlus × TransTools — case study (2026-11-10)

**Case study, not a reusable application.** Current product repo: [Centaurioun/ETKPlus-TransTools](https://github.com/Centaurioun/ETKPlus-TransTools). Bugfix [PR #5](https://github.com/Centaurioun/ETKPlus-TransTools/pull/5) remains draft and not merged.

## Main events

- Baseline test code `e3bf238fb06775e1eea9b97f494dbb99971ae55d`. Common native `window.confirm` / `prompt` code caused Word task-pane compatibility risk. Replaced by explicit in-pane confirmation on bugfix code rev `14b47fb08ef78adf3a7e3affd24619a3aa5a43ed` with successful PRO-02 real Word observation.
- Source/install manifest mismatch 3001 vs 3002; repaired and dev server started; combined causality not isolated. Panel displayed hardcoded `v1.2`, unreliable as exact code identity.
- FILE-07: untouched 208-character document preview and source DOCX selected. Clicking Apply rejects before the confirm dialog. Three `body.text` hashes equal; each OOXML digest differs with the same length 374234; first difference `w:rsidR`. Further XML differences may exist. No speculative canonicalization made.
- FILE-08: target edited with `STALE_PREVIEW_SENTINEL`. Apply-time comparison rejects and nothing appended (PASS). Old Apply button stays enabled after manual Word edit (early invalidation FAIL).
- TXT-16: 5 tab groups, 7 redundant tabs; Cancel no mutation, Confirm text changed, Undo restored tabs; latest Redo disabled and format not reviewed; prior contradictory report preserved.
- TXT-04: historical 12 preview / 11 actual range changes; later 12/12 visible cleanup, full style/Undo not verified.

## Source of truth

- [FILE-07 observations](https://github.com/Centaurioun/ETKPlus-TransTools/blob/58b0862961a69f38a2de3a1fc8fd538c470f9ae8/docs/qa/results/evidence/2026-11-10-file07-snapshot-diagnostic/observations.md)
- [Focused 18-row regression report](https://github.com/Centaurioun/ETKPlus-TransTools/blob/bf3f75fce14a774ab2026f46dec97f189f4491b7/docs/qa/results/2026-11-10-p0-regression-2026-11-10T0049%2B0300.md)
- [Observations](https://github.com/Centaurioun/ETKPlus-TransTools/blob/bf3f75fce14a774ab2026f46dec97f189f4491b7/docs/qa/results/evidence/2026-11-10-p0-followup/observations.md)
- [PR FILE-07 review note](https://github.com/Centaurioun/ETKPlus-TransTools/pull/5#issuecomment-6753375928)

## QA environment and safety

- Word for Mac, version reported 16.116 (26110342), macOS 27.0.1, arm64, taskpane https://localhost:3001/taskpane.html.
- Startup UI path: Word Blank Document → Home → Add-ins → allow Developer Add-ins loading → Word Araçlarım green tile. **Don't click More Add-ins.**
- One disposable Word document at a time; close test copies with Don't Save; no real documents or original fixture mutation.
- Reports contain text/AX observations, no persisted screenshot evidence for these runs. Do not overstate proof.

## Portability boundary

Reusable playbooks are in this KB's `troubleshooting/`. ETK-specific manifest ID, fixture data and local paths are **not** generic Office rules. Future add-ins get their own `cases/<project>/` and corresponding QA evidence rather than inheriting ETK's results.

## Local startup observations

The installed manifest historically targeted port **3002** while the current task pane and server were at **3001**. Correcting the manifest and starting the intended HTTPS server restored loading, but both changed together, so a single root cause was not isolated. On later sessions, the add-in was reopened through **Home → Add-ins → Developer Add-ins → Word Araçlarım**. These are project-specific observations, not universal Office guidance; compare exact host/build and upstream [issue #6973](https://github.com/OfficeDev/office-js/issues/6973). The active product environment and server are outside this knowledge repo's test scope.

## FILE-07 and development build identity (checked 2026-11-10)

**FILE-07 status remains FAIL with root cause unproven.** An initial Word attempt was blocked because the Developer Add-ins items were disabled and the task pane never loaded; that attempt collected no snapshots. On a later retry after the user opened the add-in, the task pane ran a local diagnostic build on one disposable synthetic 208-character target. Text remained identical across all three snapshots; OOXML lengths were 374234 characters each, while OOXML SHA-256 values differed. The first visible structural difference was `w:rsidR`, but the UI output was clipped and did not establish a complete diff or a safe normalization rule. The stale-preview preflight rejected before confirmation or append. No guard was weakened. See the [pinned FILE-07 evidence record](https://github.com/Centaurioun/ETKPlus-TransTools/blob/58b0862961a69f38a2de3a1fc8fd538c470f9ae8/docs/qa/results/evidence/2026-11-10-file07-snapshot-diagnostic/observations.md) for chronology; raw OOXML and fixtures remain outside this repository.

**Build identity finding:** at PR #5 head `58b0862961a69f38a2de3a1fc8fd538c470f9ae8`, `mac-word-mvp/package.json` declares version `1.2.0` and `taskpane.html` displays a static `v1.2` label. The dev server sends `Cache-Control: no-store`, which reduces ordinary HTTP caching but does not identify which Git revision produced a running bundle. No generated build ID or commit SHA is exposed in the task pane. Therefore the Word observation "v1.2 loaded from localhost:3001" establishes the displayed version and URL only; by itself it cannot prove the served JavaScript came from the expected commit. The next safe versioning increment is to embed a generated revision/build token in built assets and show it in a read-only diagnostics area, then compare that token with the tested checkout before native QA. This remains **a finding and proposed next step**; product files were not changed as part of knowledge-base integration.
