# [COMPONENT] — [Short symptom]

- **Case ID / owner:**
- **First observed (ISO date):**
- **Last checked (ISO date):**
- **Applicability:** Word/Office product, platform, exact version/build, OS, architecture, API requirement set; mark unknown fields explicitly.
- **Add-in identity:** version/build ID, Git SHA, served asset digest, manifest ID/URL where relevant.
- **Outcome:** PASS / FAIL / BLOCKED / NOT_RUN / NOT_SUPPORTED.
- **Evidence and confidentiality:** evidence type/path, fixture checksum; synthetic fixture only. Never store private document content, full OOXML, secrets, or private fixtures.

## Symptom
What was observed in the real host, exact error and action sequence. Separate observation from interpretation.

## Environment and version
Record complete host, OS, add-in, API-set and dependency versions. If unavailable, say so and state the scope this limits.

## Evidence
Direct measurements and their provenance. Distinguish native-host evidence, automated/mock results, and inference.

## Possible causes
Rank competing hypotheses and cite whether each is an official contract, resolved/open upstream report, local observation, or community clue. Do not call a hypothesis a root cause without proof.

## Sources
Link to relevant primary documentation, issue reports and any lower-confidence community observations. Record status and last-checked date for changeable sources.

## Safe diagnosis
Minimal non-destructive steps, counters/hashes/structural facts to collect, and confidentiality boundaries.

## Fix
Smallest change, preconditions, validation and rollback considerations. Do not claim a fix before acceptance is exercised.

## Regression
Focused automated and real-host matrix, including negative/safety checks, formatting, Undo/Redo, save/reopen where relevant. Record results per exact environment.

## Result
- **Observed outcome:**
- **Root cause:** proven / unproven / unknown, with evidence
- **Commit / PR:**
- **Remaining scope or risk:**
- **Next check:**

Never infer PASS from no error or no mutation unless the intended handler and acceptance condition were directly exercised.
