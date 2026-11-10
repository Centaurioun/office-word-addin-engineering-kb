# Word for Mac — delayed own add-in tile, avoid wrong purple Add-ins entry

**Reported by human operator: 2026-11-10. Confidence: HUMAN-OBSERVED NAVIGATION, not a Microsoft platform guarantee.** Relevant to Mac Word add-in QA using an installed XML Office.js add-in. This is a UI automation operating procedure, not a fix to Word internals.

## Observed symptom

Upon clicking **Add-ins** on Word's ribbon, the initial visible menu can show a **purple Add-ins** entry before the user's own add-in has populated. A Codex/CUA agent previously clicked the purple entry too early, pursued an incorrect flow, and required the human to intervene. The user reports that after waiting around **5–10 seconds** in the Add-ins list, their actual installed add-in appears with a **green W icon**. It is the green-W installed add-in entry, **not** the purple Add-ins item, that should be selected to launch their taskpane.

**Do not generalize colors as stable identifiers.** Colors/icons, account configuration and labels can vary across Word builds. Verify the displayed add-in name and where possible manifest/add-in identity. A generic purple Add-ins icon isn't proof of the user's installed app.

## Correct CUA / human flow (current user-observed installation)

1. In Word, click the ribbon **Add-ins** opener once; inspect its menu.
2. **Do not click purple Add-ins / marketplace / generic entry.** Wait approximately **5–10 seconds**, re-checking the menu for the user's installed add-in tile.
3. Look for the **green W** entry and verify its visible application label matches the expected ETKPlus/Word tools add-in; then click that specific entry.
4. Verify the real taskpane is open by checking its actual header and **runtime JS-embedded build identity** (e.g., dev.007), *not just that any pane or store page appeared*.
5. If the expected entry isn't present after waiting and a short recheck, record **LAUNCH_TILE_NOT_FOUND**, screen/AX observation and time elapsed; do not click the purple entry as a fallback, repeatedly open store pages, re-sideload, change manifest GUID, remove cached manifests, or install another add-in. Follow the normal [Mac startup runbook](MAC-SIDELOAD-STARTUP.md).

## Distinguish a disabled tile from a missing tile

In the 2026-11-10 P0 acceptance session, the expected named green-W tile did appear after the menu loaded, but Word exposed it as **disabled**, while the Home "Paneli Aç" ribbon command was absent. This is PRE_ACTIVATION_DISABLED, **not** LAUNCH_TILE_NOT_FOUND. Do not repeatedly click the disabled tile, invoke Marketplace, claim JS build identity, or try blocked feature controls. Follow [Mac startup procedure](MAC-SIDELOAD-STARTUP.md) and [host issue #11](https://github.com/Centaurioun/ETKPlus-TransTools/issues/11).

Historical smoke: **44 COMPLETE / 1 FAIL** (45 cases). Separate deeper acceptance after second batch: **12 PASS / 1 FAIL / 15 BLOCKED / 102 NOT_RUN** (130 cases). These are different matrices.

## Agent automation contract

- Use **observe → wait → re-observe → identify label/icon → click expected entry → verify pane/build**.
- A 5–10 s wait is an empirical UI loading time; **not** guaranteed across all Word sessions. Avoid unbounded retry loops. If timeout occurs, classify separately from a verified *Add-in Error*.
- Clicking the purple entry **does not count** as an attempted ETKPlus startup or any SMK feature test; wrong-entry interactions are test setup errors and should be corrected without labeling 44 features FAIL.
- If the previously validated **Home > Word Araçlarım > Paneli Aç** command is present, it can be used as a project-specific alternative. Record which opening path was actually used; do not silently conflate the two.
- Only interact with the QA owner's Word document and add-in. Do not change other add-ins, central deployment, manifest GUID, port, certificate or process to work around a menu delay.

## Smoke test evidence format

`date,host_build,active_worktree,app_build,open_path,click_target,wait_observed_seconds,own_tile_label,own_tile_icon,taskpane_header,runtime_build_verified,outcome,AX_or_screenshot`.

Use **COMPLETE** for the launch only after the **correct pane** is shown and build identity checked. Mark not found/loading/host errors with specific evidence, not as an unknown generic FAIL.

## Scope and case maintenance

This user's current native Word smoke report has been described as **35 COMPLETE / 1 FAIL / 1 BLOCKED / 8 NOT_RUN** (human-provided Codex report; check pushed CSV before treating it as GitHub state). This interaction note is **not** an additional Word test result.

Related [Mac sideload and startup](MAC-SIDELOAD-STARTUP.md), [QA evidence](../workflows/QA-AND-EVIDENCE.md), [ETKPlus Issue #6](https://github.com/Centaurioun/ETKPlus-TransTools/issues/6).

**Status: DOCUMENTED, NOT REPLAYED BY THIS KB AUTHOR ON THE USER'S MAC.**
