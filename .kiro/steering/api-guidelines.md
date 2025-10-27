---
inclusion: fileMatch
fileMatchPattern: "app/api/**/*"
title: API Guidelines (Developer Playbook)
summary: Practical patterns, examples, and anti-patterns for implementing our REST APIs.
tags: [api, guidelines, examples]
---

# API Guidelines (Developer Playbook)

> Practical guidance; pair with #api-standards for enforceable rules.

## Request & Response Examples

### Version Header
```http
GET /customers?limit=20 HTTP/1.1
Host: api.example.com
Authorization: Bearer <token>
X-Company-Api-Version: 2025-10-01.summit
```

### List Envelope
```json
{
  "object": "list",
  "url": "/customers",
  "has_more": true,
  "data": [{ "id":"cus_123", "object":"customer", "created":1728000000 }]
}
```

### Error Envelope
```json
{
  "error": {
    "type": "invalid_request_error",
    "code": "missing_param",
    "message": "Missing required param: email",
    "param": "email"
  }
}
```

## Idempotency Patterns
- Generate a UUID per business operation (e.g., “create payment for order 123”) and send `Idempotency-Key`.
- Cache the first response keyed by `(method, path, idempotencyKey, bodyHash)`.
- If the same key arrives with different parameters: return `409` conflict with `type=idempotency_error`.

## Validation & Errors
- Return **one** primary error with `message` and `code`. For many field errors, embed a `details` array:
```json
{ "error": { "type":"invalid_request_error", "code":"validation_failed",
  "message":"Validation failed.",
  "details":[ { "param":"email", "code":"invalid_format", "message":"Invalid email" } ] } }
```

## Webhook Consumers
- Verify HMAC signature **before** parsing JSON.
- Process asynchronously; **ack quickly** (2xx) and re-fetch object if you need authoritative state.
- Deduplicate by `event.id` and store a processed set for 72h+.

## Search vs Filter
- Prefer list filters for exact/boolean/range queries.
- Use `/search` for full-text or multi-field queries; document query DSL and limits.

## SDK Guidance
- SDKs **must** set the version header automatically and expose:
  - `withVersion("YYYY-MM-DD")` per-request override,
  - `withIdempotencyKey(uuid)` helpers,
  - automatic pagination iterators/generators.

## Observability
- Include `api_version`, `request_id`, and `idempotency_key` (if present) in structured logs.
- Emit metrics by `api_version` (latency, error rate, saturation).

---
References: #[[file:api/openapi.yaml]] #[[file:api/sdks/README.md]]
