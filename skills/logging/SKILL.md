---
name: logging
description: Structured logging, log levels, correlation ID, observability, tracing. Use when setting up logging for applications or debugging production issues.
trigger: \blogging\b.*(setup|best practice|format|structured|strategy) ;; (setup|configure|best practice).*\blogging\b ;; \blog level\b ;; \bstructured log\b ;; \bcorrelation id\b ;; \bdistributed tracing\b ;; \blog format\b.*JSON ;; \bobservability\b ;; \bOpenTelemetry\b ;; \bwinston\b ;; \bpino\b ;; \bstructlog\b ;; \bcách log\b ;; \bghi log\b.*(ứng dụng|app|server|API) ;; \blog.*(pattern|convention|standard)
---

# Logging Best Practices (Compact)

## Core Principle
**Log everything you'd need to debug a production issue at 3 AM — and nothing that would get you fired.**

## 1. Log Levels — When to Use What

| Level | When | Example |
|-------|------|---------|
| **DEBUG** | Internal state, variable values | `user_id=42, items_in_cart=3` |
| **INFO** | Key business events, milestones | `Order #1234 placed by user 42` |
| **WARN** | Recoverable anomalies, degraded | `Retry #2 for payment API (timeout)` |
| **ERROR** | Operation failed, needs attention | `Failed to charge card for order #1234` |
| **FATAL** | App cannot continue, shutdown imminent | `Database connection pool exhausted` |

## 2. Structured Logging — Always JSON

```python
# ❌ BAD — unparseable string
print(f"User {user_id} logged in at {timestamp}")

# ✅ GOOD — structured JSON
import structlog
logger = structlog.get_logger()

logger.info("user_login",
    user_id=user_id,
    ip_address=request.ip,
    auth_method="oauth2",
    session_id=session.id,
)
```

**Why JSON?** Grep-able, indexable by Elasticsearch/Loki, parseable by Datadog/Grafana.

## 3. What to Log (Always)

```python
logger.info("api_request",
    method="POST",
    path="/api/orders",
    status_code=201,
    duration_ms=142,
    user_id=user.id,
    trace_id=request.headers.get("X-Trace-Id"),
    order_id=order.id,
)
```

Every request/operation should log: **timestamp, service, trace_id, user_id, action, duration, result**.

## 4. What to NEVER Log

```python
# 🚨 SECURITY VIOLATION — these will get you fired
logger.info("user_login", password=password)           # NEVER log passwords
logger.info("payment", credit_card=card_number)        # NEVER log CC numbers
logger.info("auth", token=access_token)                # NEVER log tokens/secrets
logger.info("profile", ssn=user.ssn, dob=user.dob)     # NEVER log PII (GDPR)
```

**Redact sensitive fields** before logging. Use a sanitizer middleware.

## 5. Correlation ID — Connect the Dots

```python
import uuid

# Generate once per request, pass through all services
trace_id = str(uuid.uuid4())

# In middleware:
@app.middleware("http")
async def add_trace_id(request, call_next):
    trace_id = request.headers.get("X-Trace-Id", str(uuid.uuid4()))
    structlog.contextvars.bind_contextvars(trace_id=trace_id)
    response = await call_next(request)
    response.headers["X-Trace-Id"] = trace_id
    return response
```

**Without correlation ID:** 100 error logs from 100 services, no way to connect them.
**With correlation ID:** trace one user request end-to-end.

## 6. Node.js: Winston / Pino

```javascript
// Pino — fastest, structured by default
const pino = require('pino');
const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  redact: ['password', 'token', 'creditCard', 'req.headers.authorization'],
});

logger.info({ userId: 42, action: 'order_placed', orderId: 1234 }, 'Order placed');
```

## 7. Python: structlog

```python
import structlog, logging

structlog.configure(
    processors=[
        structlog.stdlib.filter_by_level,
        structlog.stdlib.add_log_level,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.dev.ConsoleRenderer() if DEBUG else structlog.processors.JSONRenderer(),
    ],
    context_class=dict,
    logger_factory=structlog.PrintLoggerFactory(),
)
```

## Anti-patterns
- `print()` instead of logger — can't control level, format, or destination
- Logging passwords, tokens, credit cards, PII (GDPR violation)
- `logger.error(f"Error: {e}")` without context — no stack trace, no user_id
- Logging in hot loops — every ms counts
- No correlation ID in microservices — impossible to trace requests
- `try: ... except: logger.error(...); raise` — double logging
