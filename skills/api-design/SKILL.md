---
name: api-design
description: REST API design: conventions, error handling, security. Use when designing endpoints, reviewing APIs, or building REST services.
trigger: \bREST API\b ;; \bAPI endpoint\b ;; \bAPI design\b ;; \broute\b ;; \brouter\b ;; \bcontroller\b ;; \bHTTP method\b ;; \bstatus code\b ;; \bRESTful\b ;; \bthiết kế.*API\b ;; \bAPI route\b ;; \bAPI endpoint\b ;; \bAPI design\b ;; \bREST\b.*endpoint ;; \bendpoint\b.*REST
---

# API Design (Compact)

## REST Conventions
- **Resources, not actions**: `/orders/{id}` not `/getOrder`
- **HTTP verbs**: GET(read), POST(create), PUT(replace), PATCH(partial), DELETE
- **Plural nouns**: `/orders` not `/order`
- **Nesting max 2 levels**: `/orders/{id}/items` ok; `/orders/{id}/items/{iid}/tags` too deep

## Status Codes
- `200` OK, `201` Created (with Location header), `204` No Content
- `400` Bad Request, `401` Unauthorized, `403` Forbidden, `404` Not Found
- `409` Conflict, `422` Unprocessable, `429` Rate Limit
- `500` Internal (never expose stack traces)

## Error Response Format
```json
{"error": {"code": "INVALID_INPUT", "message": "quantity must be >= 1", "field": "quantity"}}
```

## Security
- **Authorize on the object, not the route**. Route guard = logged in. Object check = THIS order belongs to THIS user.
- Same 404 for "not found" and "not yours" — don't leak existence.
- Validate on allowlist, not denylist.
- Rate-limit auth endpoints. Use constant-time comparison for secrets.

## Pagination
- Cursor-based > offset-based for large datasets.
- Return `next_cursor`, `has_more`. Never return total count on large tables.