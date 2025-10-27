---
inclusion: manual
title: API Security Policies
summary: Authn/z, secrets, and transport security guidelines.
tags: [security, api]
---

# API Security Policies

- **TLS required** (min 1.2). Enable HSTS.
- **Token scopes**: define least-privilege scopes per resource/action; deny by default.
- **Key rotation**: automate rotation for service accounts; audit every 90 days.
- **Secrets handling**: never commit secrets; use a vault. Rotate webhook signing secrets with overlap.
- **Input sanitization**: validate and canonicalize; reject unknown params when `strict=true` is set.
- **PII**: do not return PII in errors; log with redaction. Mask sensitive fields in traces.
- **Rate limiting**: enforce per-token and per-IP; return `429` with `Retry-After`.

> Include this file in chats when needed: `#security-policies`.
