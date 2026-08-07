---
name: database
description: Database design & SQL optimization: schema design, indexing, query tuning, migrations. Use when working with SQL, PostgreSQL, MySQL, or database schemas.
trigger: \bdatabase\b ;; \bSQL\b ;; \bPostgreSQL\b ;; \bMySQL\b ;; \bschema\b ;; \bindex\b ;; \bmigration\b ;; \bquery\b ;; \bjoin\b ;; \btransaction\b ;; \bACID\b ;; \bORM\b ;; \bcơ sở dữ liệu\b ;; \btối ưu query\b ;; \btối ưu SQL\b ;; \btối ưu database\b ;; \btối ưu DB\b
---

# Database (Compact)

## Schema Design
- **Normalize to 3NF** — eliminate redundancy, ensure data integrity.
- **Denormalize deliberately** — only when profiling proves it's needed.
- **UUID vs auto-increment**: UUID for distributed, auto-increment for single-DB (smaller indexes).
- **Timestamps**: always `created_at` + `updated_at`. Use `timestamptz`, never `timestamp` without TZ.
- **Soft delete** (`deleted_at`) only when audit trail required — complicates every query.

## Indexing Rules
```sql
-- 1. Index every foreign key
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- 2. Composite index: equality first, range last
CREATE INDEX idx_orders_user_status_date ON orders(user_id, status, created_at DESC);
-- Query: WHERE user_id = ? AND status = ? ORDER BY created_at DESC

-- 3. Partial index for filtered queries
CREATE INDEX idx_active_orders ON orders(created_at) WHERE status = 'active';

-- 4. Covering index (include non-key columns) — PostgreSQL 11+
CREATE INDEX idx_orders_user_cover ON orders(user_id) INCLUDE (total, status);
```

## Query Optimization
```sql
-- Always EXPLAIN ANALYZE before shipping
EXPLAIN ANALYZE SELECT ...;

-- Red flags in EXPLAIN:
-- "Seq Scan" on large table → missing index
-- "Nested Loop" with high rows → missing index on join column
-- "Sort" with high cost → add index matching ORDER BY
```

## N+1 Problem
```python
# ❌ N+1: 1 query for users + N queries for orders
users = db.query("SELECT * FROM users")
for user in users:
    orders = db.query("SELECT * FROM orders WHERE user_id = ?", user.id)

# ✅ JOIN or eager load
users = db.query("SELECT u.*, o.* FROM users u LEFT JOIN orders o ON u.id = o.user_id")
# ORM: User.query.options(joinedload(User.orders)).all()
```

## Migrations
- **Always reversible** — every `up` must have a working `down`.
- **Never backfill in a migration** — use a separate script/job.
- **Add column with default**: dangerous on large tables (locks). Add nullable → backfill → set default → set NOT NULL.
- **Rename column**: add new, dual-write, backfill, switch reads, drop old.

## Anti-patterns
- `SELECT *` in production — fetch only needed columns.
- No pagination on list queries — `LIMIT` + cursor.
- Missing foreign keys — data integrity at app layer is a lie.
- `LIKE '%term%'` — leading wildcard disables index. Use full-text search.
- Large `IN (...)` clauses — use `JOIN` or temp table instead.