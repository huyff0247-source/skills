---
name: code-review
description: Review code for bugs, security issues, and best practices. Use when user asks for code review, feedback on code, or code quality check.
trigger: \bcode review\b ;; \bkiểm tra code\b ;; \baudit code\b ;; \bđánh giá code\b ;; \bpull request\b ;; \bPR review\b ;; \breview code\b ;; \breview PR\b
---

# Code Review Skill

When reviewing code, follow this process:

1. **Understand the purpose**: First, determine what the code is supposed to do.
2. **Check for bugs**: Look for logical errors, off-by-one errors, null/undefined handling, race conditions.
3. **Security review**: Check for common vulnerabilities (SQL injection, XSS, insecure deserialization, hardcoded secrets).
4. **Performance**: Identify obvious bottlenecks (N+1 queries, unnecessary loops, large memory allocations).
5. **Best practices**: Check naming conventions, code organization, error handling, documentation.
6. **Provide actionable feedback**: For each issue, explain:
   - What the problem is
   - Why it matters
   - How to fix it (with code example)

Output format:
```
## Code Review: [filename]

### Summary
Brief overall assessment (1-2 sentences)

### Issues Found
1. **[Severity] Issue title** (line X-Y)
   - Problem: ...
   - Impact: ...
   - Fix: ```code example```

### Strengths
- What the code does well

### Suggestions
- Improvements that aren't bugs but would make code better
```
