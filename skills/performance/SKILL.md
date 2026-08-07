---
name: performance
description: Performance optimization: profiling, bottlenecks, and when NOT to optimize. Use when code is slow, profiling, or optimizing.
trigger: \bperformance\b.*(code|chậm|slow|hàm|function|loop|query|\boptim|bottleneck|profile|lag|timeout) ;; (code|hàm|function|loop|query|app|server|web|database|SQL).*\bperformance\b ;; \boptimize\b ;; \btối ưu\b ;; \bbottleneck\b ;; \bprofile\b ;; \bprofiling\b ;; \bnhanh hơn\b ;; \blag\b ;; \btimeout\b ;; \bchậm\b ;; \bslow\b ;; \bcode.*chậm\b ;; \bcode.*slow\b ;; \bhàm.*chậm\b ;; \bhàm.*slow\b
---

# Performance Protocol (Compact)

## Golden Rules
1. **Measure first**. Never optimize without a profile.
2. **Optimize the bottleneck**. 80/20 rule: 80% of time in 20% of code.
3. **Test after**. Optimization often introduces bugs.

## Common Bottlenecks & Fixes
| Symptom | Likely Cause | Fix |
|---|---|---|
| Slow page load | N+1 queries | `JOIN` / `select_related` / `eager_load` |
| High CPU | O(n²) nested loops | Hash map / set lookup |
| Memory spike | Unbounded collection | Pagination / streaming / generators |
| Slow API response | Blocking I/O | Async / connection pooling |
| DB timeout | Missing index | `EXPLAIN ANALYZE` → add index |
| Slow startup | Heavy imports | Lazy import / deferred init |

## Profiling Quick Ref
- **Python**: `cProfile` + `snakeviz`, `line_profiler` for per-line
- **SQL**: `EXPLAIN ANALYZE` before every query change
- **Web**: browser DevTools Network/Performance tab
- **Memory**: `memory_profiler`, `tracemalloc`

## Anti-patterns
- Optimizing without measuring (solving wrong problem).
- Caching without invalidation strategy.
- Micro-optimizations (loop unrolling, bit tricks) — compiler already does this.
- Premature optimization — make it work, make it right, THEN make it fast.