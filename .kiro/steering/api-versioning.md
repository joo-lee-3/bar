---
inclusion: always
title: API Versioning (Header + Lifecycle)
summary: Date versioning, pinning, and lifecycle rules.
tags: [api, versioning, lifecycle]
---

# API Versioning (Header + Lifecycle)

## 1) Identifier & Selection
- **MUST** format version as `YYYY-MM-DD[.codename]` (e.g., `2025-09-13.summit`).
- **MUST** accept `X-Company-Api-Version` header; if absent, use the **app’s pinned** default (stored in developer portal/config).
- **MAY** allow environment-level default pinning (sandbox vs prod).

## 2) Compatibility Policy
- **Additive-only in-version**: adding optional request params, response fields, new endpoints = **no new version**.
- **Breaking requires new version**: field removal/rename, type change, semantics change, required param, error contract change.

## 3) Support & Deprecation
- **Support window:** >= **24 months** from release.
- **Notices:** **T-24 / T-12 / T-6 / T-1 months** via changelog and dev portal; emit `Deprecation`/`Sunset` headers.
- **Frozen window:** last 6 months accept security/critical fixes only.

## 4) Testing & Rollouts
- **MUST** support per-request override for canaries and A/Bs.
- **MUST** provide test/sandbox with any version.
- **MUST** run CI contract tests for **all supported versions**.

## 5) Localized Path Versions (Rare)
- **MAY** introduce `/v2/<resource>` only when a resource redesign cannot be expressed via header versioning.
- **MUST** document the interaction between path `v2` and header version; provide a migration guide and dual-run window.

---
References: #[[file:docs/version-matrix.md]] #[[file:docs/api-changelog.md]]
