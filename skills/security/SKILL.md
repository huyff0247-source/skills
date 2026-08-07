---
name: security
description: Security best practices: OWASP Top 10, authentication, authorization, cryptography, secrets management. Use when handling auth, sensitive data, or security review.
trigger: \bsecurity\b ;; \bbảo mật\b ;; \bOWASP\b ;; \bauth\b ;; \bauthentication\b ;; \bxác thực\b ;; \bpassword\b ;; \bmật khẩu\b ;; \btoken\b ;; \bJWT\b ;; \bencrypt\b ;; \bmã hóa\b ;; \bSQL injection\b ;; \bXSS\b ;; \bCSRF\b ;; \bsecret\b ;; \bsecurity review\b ;; \bsecurity audit\b ;; \bsecurity check\b
---

# Security (Compact)

## OWASP Top 10 Quick Defense
| # | Vulnerability | Defense |
|---|---|---|
| 1 | Broken Access Control | Authorize on object, not route |
| 2 | Cryptographic Failures | Argon2id, AES-GCM, TLS 1.3 |
| 3 | Injection | Parameterized queries, never concatenate |
| 4 | Insecure Design | Threat model before coding |
| 5 | Security Misconfig | Hardened defaults, CSP, HSTS |
| 6 | Vulnerable Components | `npm audit`, `pip-audit`, dependabot |
| 7 | Auth Failures | Rate-limit login, MFA, secure sessions |
| 8 | Software/Data Integrity | Verify checksums, pin dependencies |
| 9 | Logging & Monitoring | Log auth events, alert on anomalies |
| 10 | SSRF | Validate URLs, block internal IPs |

## Authentication
```python
# Password hashing — Argon2id (winner of Password Hashing Competition)
from argon2 import PasswordHasher
ph = PasswordHasher()  # correct defaults: time_cost=3, memory_cost=65536

# NEVER: MD5, SHA-1, SHA-256(raw), bcrypt with low rounds
# ALWAYS: Argon2id > bcrypt(cost≥12) > scrypt
```

## Authorization
```python
# ❌ Route-level only — checks login, not ownership
@requires_auth
def get_order(order_id): ...

# ✅ Object-level — every access verified
def get_order(order_id, user):
    order = db.orders.get(order_id)
    if not order or order.user_id != user.id:
        raise HTTPException(404)  # same 404 for "not found" and "not yours"
    return order
```

## Secrets Management
```
❌ Hardcoded:     API_KEY = "sk-abc123"
❌ In .env committed: .env in git
❌ In Dockerfile:  ENV DATABASE_URL=...
✅ Environment at runtime (not in image)
✅ Secrets manager: HashiCorp Vault, AWS Secrets Manager, Doppler
✅ .env.example (template only) + .env in .gitignore
```

## Quick Security Checklist
- [ ] All queries parameterized (no string interpolation)
- [ ] All output encoded (HTML entities, JSON properly)
- [ ] Auth on every sensitive endpoint
- [ ] Rate limiting on login/registration
- [ ] CORS restricted to known origins
- [ ] CSP header set
- [ ] Cookies: `HttpOnly; Secure; SameSite=Lax`
- [ ] Dependencies audited (`npm audit`, `pip-audit`)
- [ ] No secrets in code, logs, or error messages
- [ ] TLS everywhere (HSTS preload)

## Anti-patterns
- Rolling your own crypto — use libsodium / WebCrypto.
- JWT with `alg: none` accepted — pin the algorithm server-side.
- Timing leak on secret comparison — use `hmac.compare_digest()`.
- Logging passwords/tokens — redact in log formatter.
- Trusting client-side validation — server must re-validate everything.