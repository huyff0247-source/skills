---
name: error-handling
description: Error handling patterns: retry, circuit breaker, graceful degradation, fallback, timeout. Use when designing resilient systems or handling API failures.
trigger: \bretry\b.*(pattern|strategy|logic|API|request|call) ;; (pattern|strategy|logic).*\bretry\b ;; \bcircuit breaker\b ;; \bgraceful degradation\b ;; \bfallback\b.*(strategy|pattern|mechanism) ;; \bexponential backoff\b ;; \bretry with jitter\b ;; \bbulkhead\b.*pattern ;; \berror handling\b.*(pattern|best practice) ;; \bresilience\b.*(pattern|design) ;; \bxử lý lỗi\b.*(API|timeout|network|retry|circuit) ;; \bchịu lỗi\b
---

# Error Handling Patterns (Compact)

## Core Principle
**Failures are inevitable. Design for them, don't just catch exceptions.**

## 1. Retry Pattern

```python
import time, random

def retry_with_backoff(func, max_retries=3, base_delay=1.0):
    """Exponential backoff + jitter to avoid thundering herd."""
    for attempt in range(max_retries):
        try:
            return func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            delay = base_delay * (2 ** attempt)  # 1s, 2s, 4s
            jitter = random.uniform(0, delay * 0.3)  # ±30% jitter
            time.sleep(delay + jitter)
```

**Rules:**
- Only retry **idempotent** operations (GET, PUT, idempotent POST)
- Never retry on 4xx client errors (except 429 rate limit)
- Always add **jitter** — synchronized retries cause thundering herd
- Set a **max total time**, not just max retries

## 2. Circuit Breaker

```
States: CLOSED → OPEN → HALF_OPEN → CLOSED
         (normal)  (fail fast)  (probe)     (recovered)
```

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, recovery_timeout=30):
        self.failures = 0
        self.threshold = failure_threshold
        self.recovery = recovery_timeout
        self.state = "CLOSED"
        self.last_failure_time = None

    def call(self, func):
        if self.state == "OPEN":
            if time.time() - self.last_failure_time > self.recovery:
                self.state = "HALF_OPEN"
            else:
                raise Exception("Circuit breaker OPEN — failing fast")

        try:
            result = func()
            if self.state == "HALF_OPEN":
                self.state = "CLOSED"
                self.failures = 0
            return result
        except Exception:
            self.failures += 1
            self.last_failure_time = time.time()
            if self.failures >= self.threshold:
                self.state = "OPEN"
            raise
```

## 3. Graceful Degradation

When a dependency fails, return **degraded but functional** response instead of crashing:

```python
def get_user_profile(user_id):
    try:
        avatar = avatar_service.get(user_id)  # optional dependency
    except Exception:
        avatar = DEFAULT_AVATAR  # fallback

    try:
        posts = post_service.get_recent(user_id)
    except Exception:
        posts = []  # empty list is acceptable degradation

    return {"id": user_id, "avatar": avatar, "posts": posts}
```

## 4. Timeout — Always Set One

```python
# HTTP
requests.get(url, timeout=(3.0, 10.0))  # (connect, read)

# Python
result = future.result(timeout=5.0)

# Database
db.execute("SELECT ...", timeout=30)
```

**Never:** infinite wait, default timeout, or timeout=0.

## 5. Bulkhead Pattern

Isolate failures like ship compartments — one service failure shouldn't sink the whole system:

```python
from concurrent.futures import ThreadPoolExecutor

# Separate thread pools for different dependencies
db_executor = ThreadPoolExecutor(max_workers=10)
email_executor = ThreadPoolExecutor(max_workers=2)  # limited, slow service

def query_db(query):
    return db_executor.submit(db.execute, query)

def send_email(to, body):
    return email_executor.submit(email_service.send, to, body)
```

## Anti-patterns
- `except Exception: pass` — silent failure, impossible to debug
- `except: ...` without specifying exception type
- Retrying non-idempotent operations (duplicate charges, double-send emails)
- No timeout → thread hangs forever
- Retry without backoff → DDoS your own dependency
