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

## ADR-007: Dual-surface instead of replacing taskpane (2026-11-10)
**Decision:** Word ribbon buttons and one-level dropdown menus are primary discoverable entry points, while the right taskpane remains the rich workbench. Do not migrate all current controls immediately. Existing Word adapters and pure rules stay shared, reached by a single feature dispatcher. See [Six-cycle product refinement](guides/SIX-CYCLE-PRODUCT-REFINEMENT-2026-11-10.md). Product code not yet changed.

## ADR-008: Keep existing XML for now
**Decision:** Current XML add-in-only manifest already permits custom ribbon menus and one custom core tab. Unified JSON is not a simple "newest is best" replacement; use a separate migration decision when distribution needs arise. No manifest changes in this review.

## ADR-009: Portable skill revision with pending native evaluation
**Decision:** Update the main `office-word-addin-engineering` router and add bundled references and 10 pressure scenarios, retaining old skill SHA in the audit record. Static schema and link checks do not substitute for new Codex runtime discovery and RED/GREEN tests. See [Skill refinement](guides/SIX-CYCLE-SKILL-REFINEMENT-2026-11-10.md).

## ADR-010: Source over collection
**Decision:** Catalog official Word samples, current skills spec and suitable open-source architecture patterns; do **not** install third-party Word AI agent packages or arbitrarily copy unreviewed code. Keep confidential manuscript data out of model-backed automation without explicit opt-in.
