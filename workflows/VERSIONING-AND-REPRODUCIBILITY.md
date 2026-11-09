# Word add-in runtime build identity — product version is not enough

**Observed problem:** Word shows a hardcoded `v1.2 — deneme` in taskpane HTML and `package.json` remains `1.2.0` across multiple code revisions. A developer therefore cannot prove that reload loaded new compiled JS.

## Required identities

- **Product SemVer:** e.g. `1.2.0`; bump only for deliberate patch/minor/major product releases. Don't change GUID or manifest for a new dev build.
- **Monotonic QA candidate ID:** e.g. `1.2.0-dev.008`. Increment for each **distinct code-changing test candidate**, not every page refresh, screenshot or identical rebuild. Reserve and persist this counter with reproducible build automation; avoid racing on parallel worktrees.
- **Code provenance:** short full Git SHA, dirty flag and deterministic digest of changed relevant source (for uncommitted builds). A HEAD SHA alone is insufficient when local source is dirty.
- **Asset identity:** compiled module or bundle cryptographic SHA-256; build time UTC; optionally manifest/source-location fingerprint.
- **Host identity:** Word build, OS, requirement set, port and tested fixture ID separately from app version.

## Minimal design rules

1. Build process creates generated build metadata from the actual source candidate. Do not rely on an untracked local environment variable for the only identifier.
2. Embed build ID + source fingerprint in the actual **compiled JS module** and render that identity **at runtime** into a dedicated taskpane DOM element. The HTML should initially show `Loading build…` (not a misleading fixed version).
3. Preserve dev-server no-store responses (Microsoft [troubleshooting guidance](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/troubleshoot-development-errors) suggests `Cache-Control: private, no-cache, no-store`, `Pragma: no-cache` and `Expires: -1` for stale static assets). Consider script URL cache-busting/tagged filenames where supported without breaking manifest.
4. Record the **actual served JS digest** and the displayed runtime identifier. If they don't match expected candidate, **BLOCK** QA: the new code is not proven loaded.
5. Show details in an accessible footer, for example `v1.2.0-dev.008 · SHA 58b0862 · clean/dirty · UTC build`; offer Copy diagnostic (without secrets or user document data).
6. Keep source branch, dev server worktree, manifest, HTTPS port **3001** and Word UI selected tile aligned. Avoid changing package-lock/manifest on each experiment.
7. A failed QA run must still record the candidate ID, not just `v1.2`.

## Test cases

| Condition | Expected |
|---|---|
| Rebuild identical checked-in code | Same fingerprint; ID reuse allowed if no new candidate |
| Change TS + rebuild | Distinct candidate and served JS digest; Word displays new ID |
| Change static HTML/CSS | Distinct asset fingerprint and appropriate candidate ID |
| Test compiled but Word stale/cached | Runtime shows old ID → BLOCKED; no false claims |
| Dirty uncommitted source | `dirty.<digest>` visible; not misidentified as clean commit |
| New product release | SemVer bumped intentionally, dev sequence reset/advanced by documented policy |

## Notes

Avoid recursive build metadata churn where generating metadata changes its own fingerprint. Use tracked source inputs with excluded generated file(s), deterministic hashing and one authoritative build manifest. Never claim a *commit SHA* identifies uncommitted diagnostic code. The final production artifact needs its own provenance identity.
