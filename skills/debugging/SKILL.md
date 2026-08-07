---
name: debugging
description: Debugging protocol: hypothesis-driven root-cause analysis. Use when investigating bugs, errors, crashes, or unexpected behavior.
trigger: \bdebug\b ;; debugging ;; \blỗi\b.*(code|function|script|program|app|server|runtime|compile|syntax|logic|memory) ;; (code|function|script|program|app|server).*\blỗi\b ;; \bbug\b.*(code|function|program|script|app|software|fix|sửa) ;; (code|function|program|script|app).*\bbug\b ;; \bfix bug\b ;; \bsửa lỗi\b ;; \bcrash\b ;; \bkhông chạy\b ;; \bexception\b ;; \btraceback\b ;; \bstack trace\b ;; \btại sao.*lỗi\b ;; \btại sao.*không chạy\b
---

# Debugging Protocol (Compact)

## Core Method: Binary Search over Hypotheses

1. **Reproduce** — smallest input that reliably fails. If flaky, run in loop for failure rate.
2. **Read evidence** — entire stack trace, every line. Not what you *assume* it says.
3. **One hypothesis** — falsifiable claim: "X causes Y because Z". Test with instrumentation, not code changes.
4. **Bisect** — `git bisect`, comment out half the input, disable half the config.
5. **Prove cause** — you have it when you can turn the bug on/off at will.
6. **Fix + regression test** — write failing test first, then fix.

## Rules
- Never change 2 things at once. You won't know which mattered.
- "It works now" without explanation = bug still there.
- Trust the running code, not your memory of it.
- Concurrency bugs → look for shared mutable state first.
- Delete debug prints after; promote useful ones to structured logging.