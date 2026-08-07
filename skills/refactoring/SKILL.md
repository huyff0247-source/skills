---
name: refactoring
description: Safe refactoring: change structure without changing behavior. Use when restructuring code, reducing technical debt, or preparing for a feature.
trigger: \brefactor\b ;; \bcấu trúc lại\b ;; \bclean code\b ;; \btechnical debt\b ;; \brestructure\b ;; \bcải thiện code\b ;; \btái cấu trúc\b
---

# Refactoring Protocol (Compact)

## Constraint: Behavior must NOT change
If behavior changes, it's a rewrite — needs different scrutiny.

## Sequence
1. **Justify** — name the concrete cost. "It's ugly" is not a cost. "Every new payment method edits 5 files" is.
2. **Pin behavior** — characterization tests: record what code actually does, assert that. Bugs included.
3. **Small steps** — one mechanical transformation per commit. Run tests after each.
4. **Separate commits** — refactor commit ≠ feature commit. Reviewers can't verify both.
5. **Stop when ready** — refactoring is preparation, not destination.

## Safe Transformations
- Extract function / class / variable
- Inline function / variable
- Replace conditional with polymorphism
- Introduce parameter object
- Break dependency via seams (inject DB, clock, network)

## Rules
- Never refactor without tests you trust.
- If tests need to change → you're rewriting, not refactoring.
- Delete dead code aggressively. Git remembers it.
- Automated IDE refactors > hand edits.
- Resist wholesale rewrites. Old code encodes years of edge cases.