---
inclusion: always
title: API Governance
summary: Change management, approvals, and deprecation workflow for APIs.
tags: [api, governance, standards, process]
---

# API Governance

## 1) Change Categories
- **Additive Change (Safe)**
  - Examples: new optional fields, new endpoints, new error codes, new enum values.
  - Approval: Team Lead.
  - Artifacts: Changelog entry.

- **Breaking Change**
  - Examples: removing/renaming fields, type/semantics changes, making optional fields required, changing error schema.
  - Approval: API Center of Excellence (CoE).
  - Artifacts: ADR, Changelog, Migration Guide, Contract Tests updated.

- **Deprecation**
  - Examples: fields marked deprecated before removal.
  - Approval: Team Lead + API CoE review.
  - Artifacts: Changelog, deprecation notice, timeline (24/12/6/1 months).

## 2) Required Artifacts
- **ADR Template:** #[[file:docs/adr-template.md]]
- **Changelog:** #[[file:docs/api-changelog.md]]
- **Migration Guide:** #[[file:docs/migration-guides/migration-guide-template.md]]
- **Version Matrix:** #[[file:docs/version-matrix.md]]

## 3) Decision Workflow
1. Engineer drafts proposal and identifies change type.
2. Submit ADR if breaking change.
3. Team Lead reviews additive changes; API CoE reviews breaking/deprecations.
4. Merge only after required artifacts and approvals are complete.

## 4) Deprecation Policy
- **Minimum support window:** 24 months for any version.
- **Communication:** Notices at 24, 12, 6, and 1 months before sunset via changelog and developer portal.
- **Frozen period:** Last 6 months of support → critical fixes only.

## 5) CI/CD Integration
- **MUST** run contract tests across all supported versions in CI.
- **MUST** validate error schema, pagination, and idempotency rules.
- **MUST** block merges if governance artifacts (ADR, changelog entry) are missing.

---
References: #[[file:docs/architecture/adr-index.md]] #[[file:api/openapi.yaml]]
