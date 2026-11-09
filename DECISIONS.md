# Curatorial engineering decisions — 2026-11-10

## ADR-001: Knowledge repository separate from application product
**Decision:** maintain reusable troubleshooting docs + Codex skill in a **dedicated private GitHub repository** after staging. Avoid copying original Word documents, full OOXML, credentials, sample binaries or unreviewed vendor code. ETK product repo may link to an exact KB revision.

## ADR-002: Runtime build identity distinct from product SemVer
**Decision:** monotonic `dev.N` for each distinct testable candidate, plus embedded JS build SHA/dirty source digest and asset fingerprint. Product `1.2.0` is not bumped for every temporary test. No manifest GUID edits.

## ADR-003: Stale-preview protection cannot be disabled for usability
**Decision:** early UI invalidation best effort, mutation-time guard mandatory and fail-closed. FILE-07 false stale detection does not justify text-only security or direct mutator calls. Narrow canonicalization requires full structural diff proof and adverse-edit tests.

## ADR-004: Office.js mocks don't replace native Word QA
**Decision:** separate automated unit/tool verification from AX/screenshot/native Word evidence. Preserve historical contradictions and test host identity.

## ADR-005: Adopt official tools only on demonstrated value
**Decision:** Microsoft Learn docs/MCP, Office.js API reference, GitHub issue trackers, transient manifest validation and tailored skill are high-value. `office-addin-debugging` can alter sideload; `office-addin-mock`, test-server/helpers, lint are deferred without isolated pilot. The Microsoft Office Developer AI Tools repo is a curated source, not an automatic skill installer.

## ADR-006: Future AI features are explicitly a different phase
**Decision:** ToolRegistry + ContextResolver from Microsoft Community Learning demo are architectural inspiration for a later assistant, not P0 fix. Never send unpublished academic, clinical or patient documents to external LLM by default, and require explicit human review/confirmation for mutation.

## Maintenance rule
When new Microsoft/Reddit/GitHub evidence appears, record **retrieval date, link, issue status, host build and whether directly reproduced**. Don't silently upgrade a hypothesis to a root cause or a former host bug to a current one.
