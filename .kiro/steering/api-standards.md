---
inclusion: always
title: API Standards (REST)
summary: Canonical, enforceable REST API rules for all services. API-as-a-Product approach.
tags: [api, rest, standards, governance]
---

# API Standards (REST)

> **Intent:** Enforceable rules (MUST/SHOULD/MAY) for REST APIs. Use alongside #api-guidelines for examples and anti-patterns.

## 1) Versioning (Header-based, Date Snapshot)
- **MUST** select API version via request header: `X-Company-Api-Version: YYYY-MM-DD[.codename]`.
- **MUST** pin each application (client) to a default version server-side; header overrides per request.
- **MUST** treat versions as **contract snapshots**; only ship **additive** (non-breaking) changes within a version.
- **MUST** open a **new version** for breaking changes (removals, type/semantics change, required params, error shape changes).
- **SHOULD** provide >= **24 months** support for each version with deprecation notices at 24/12/6/1 months.
- **MUST** expose `Deprecation`/`Sunset` headers on deprecated versions.

> Live spec: #[[file:api/openapi.yaml]]

## 2) Resource Naming & URLs
- **MUST** use plural, noun-based resource names: `/customers`, `/payment_intents`, `/event_destinations`.
- **MUST** use flat top-level resources; nest only for **true containment** (e.g., `/customers/{id}/sources`).
- **MUST NOT** encode operations in names; prefer action subpaths, e.g., `/charges/{id}/capture`.
- **MUST** keep URLs **stable** across versions (version chosen by header, not path).
- **MAY** provide `/v2/...` **only** for localized resource redesigns when header-versioning alone is insufficient (document rationale + migration).

## 3) HTTP Methods & Semantics
- **GET** retrieve/list; **POST** create **and** partial updates (organization standard); **DELETE** delete.
- **MUST** be idempotent for **GET/DELETE**.
- **SHOULD** use:
  - `200 OK` for successful GET and POST updates,
  - `201 Created` for creates,
  - `204 No Content` for deletions with no body.

## 4) Authentication & Authorization
- **MUST** require HTTPS; reject HTTP.
- **MUST** use bearer-style auth: `Authorization: Bearer <token>`; tokens are scoped (least privilege).
- **MAY** support Basic with secret key for service-to-service if rotation & scope controls exist.
- **MAY** support delegated / on-behalf-of via header (e.g., `X-Company-Account: acct_...`) with strict validation.
- **MUST** return `401` for missing/invalid auth, `403` for insufficient scope/permission.

## 5) Pagination (Cursor-based)
- **MUST** implement cursor-based pagination on list endpoints:
  - Query: `limit` (default 10, max 100), `starting_after`, `ending_before` (object IDs).
  - Response envelope:
    ```json
    { "object": "list", "url": "/customers", "has_more": true, "data": [ ... ] }
    ```
- **MUST** default sort to **created desc** (newest first).
- **SHOULD NOT** expose offset-based paging on mutable datasets.

## 6) Filtering & Search
- **MAY** allow simple filters via query (e.g., `?email=...`, `?status=...`, `?created[gt]=...`).
- **SHOULD** provide separate `/search` endpoints for complex queries with a query language.
- **MUST** document supported filters for each endpoint.

## 7) Idempotency (Writes)
- **MUST** accept `Idempotency-Key` on **POST** (and PUT/PATCH if used) to guarantee safe retries.
- **MUST** return the **original response** for repeated requests with the same key+parameters (including error).
- **MUST** return a specific **idempotency error** (`409`) if the same key is reused with different parameters.
- **MUST NOT** process idempotency keys for **GET/DELETE**.

## 8) Error Model
- **MUST** return JSON error envelope:
  ```json
  {
    "error": {
      "type": "invalid_request_error | api_error | auth_error | rate_limit_error | idempotency_error | domain_error",
      "code": "short_machine_code",
      "message": "Human-readable message.",
      "param": "fieldName (optional)",
      "details": [ { "param": "field", "code": "invalid", "message": "..." } ]
    }
  }
  ```
- **MUST** map to HTTP:
  - 400 invalid request / validation
  - 401 unauthorized
  - 402 domain-specific request failed (optional)
  - 403 forbidden
  - 404 not found
  - 409 conflict (including idempotency conflicts)
  - 429 rate limited
  - 5xx server errors
- **SHOULD** keep messages human-readable and free of sensitive data.

## 9) Webhooks
- **MUST** sign deliveries (HMAC) and require signature verification; reject if invalid.
- **MUST** deliver **Event** schema with unique `id`, `type`, `created`, `data.object` (and `data.previous_attributes` where applicable).
- **MUST** implement exponential retry/backoff for up to 72 hours; recipients **must** be idempotent by `event.id`.
- **MUST** require a 2xx within timeout; otherwise retry.

## 10) Documentation & Changelog
- **MUST** publish per-version OpenAPI & human changelog (with machine-readable diff).
- **MUST** provide migration guides for breaking versions.
- **SHOULD** include multi-language snippets and SDKs that auto-set version header.

## 11) Governance Hooks
- **MUST** obtain API CoE approval for breaking changes.
- **MUST** run version-aware contract tests in CI for all supported versions.
- **MUST** update changelog, version matrix, and ADR index upon merge.

---
References: #[[file:api/openapi.yaml]] #[[file:docs/api-changelog.md]] #[[file:docs/version-matrix.md]]
