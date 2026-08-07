---
name: testing
description: Test strategy: what to test, at which level, and how to avoid brittle tests. Use when writing tests, unittest, pytest, or designing test suites.
trigger: \bunit\s?test\b ;; \bpytest\b ;; \bunittest\b ;; \bcoverage\b ;; \bkiểm thử\b ;; \bmock\b ;; \bassert\b ;; \bTDD\b ;; \btest suite\b ;; \btest case\b ;; \bviết test\b ;; \bwrite test\b
---

# Testing Strategy (Compact)

## Golden Rule: Test behavior, not implementation
A test that breaks when you rename a private method prevents refactoring.

## Test Levels
- **Unit (~70%)**: Business logic, edge cases, error paths. Milliseconds. Use real deps where practical.
- **Integration (~25%)**: Real DB, real queue. Seconds. Catch SQL errors mocks miss.
- **E2E (~5%)**: Critical user journeys only. Tens of seconds.

## What To Test
- Boundaries: empty list, null, concurrent write, network failure.
- After every production bug → write the test that would have caught it.
- Happy path is the least valuable test — it already works.

## Anti-patterns
- Mocking your own repository layer (tests only that mock is configured).
- Asserting on log messages or internal call counts.
- 100% coverage target → leads to assertion-free tests.
- Flaky tests → quarantine immediately, fix as defect.

## Structure
```python
# Good: Arrange → Act → Assert, tests behavior
def test_refund_reduces_balance(db):
    order = place_order(db, total=5000)
    result = service.refund(order.id, 2000)
    assert db.orders.get(order.id).refunded == 2000
```