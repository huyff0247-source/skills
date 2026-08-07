---
name: system-design
description: System design: architecture patterns, scalability, databases, caching, message queues, microservices. Use when designing systems, discussing architecture, or preparing for system design interviews.
trigger: \bsystem design\b ;; \barchitecture\b ;; \bkiến trúc hệ thống\b ;; \bscalable\b ;; \bmicroservice\b ;; \bmonolith\b ;; \bdatabase design\b ;; \bmessage queue\b ;; \bcaching strategy\b ;; \bload balancer\b ;; \bCDN\b ;; \bdistributed\b ;; \bthiết kế hệ thống\b ;; \bhệ thống.*scale\b ;; \bhệ thống.*phân tán\b
---

# System Design (Compact)

## Architecture Patterns
| Pattern | When | Trade-off |
|---|---|---|
| **Monolith** | Early stage, small team | Simple deploy, hard to scale team |
| **Microservices** | Large team, independent deploys | Complex ops, eventual consistency |
| **Serverless** | Variable load, event-driven | Cold starts, vendor lock-in |
| **Event-driven** | Async workflows, decoupling | Debugging complexity, ordering |

## Database Selection
| Type | Use Case | Examples |
|---|---|---|
| **Relational (SQL)** | Structured data, ACID, joins | PostgreSQL, MySQL |
| **Document (NoSQL)** | Flexible schema, nested data | MongoDB, Firestore |
| **Key-Value** | Caching, sessions, counters | Redis, DynamoDB |
| **Columnar** | Analytics, time-series | ClickHouse, Cassandra |
| **Graph** | Highly connected data | Neo4j, DGraph |
| **Search** | Full-text search | Elasticsearch, Meilisearch |

## Caching Layers
```
Client (Browser Cache / Service Worker)
  → CDN (Cloudflare, CloudFront)
    → API Gateway Cache
      → Application Cache (Redis, Memcached)
        → Database
```
- **Cache-Aside**: app checks cache → miss → DB → populate cache.
- **Write-Through**: write to cache + DB simultaneously.
- **Write-Behind**: write to cache → async to DB (faster, riskier).
- **TTL**: always set expiry. No TTL = stale data forever.

## Scaling Strategies
1. **Vertical**: bigger server (easy, limited).
2. **Horizontal**: more servers + load balancer.
3. **Read replicas**: scale reads, writes go to primary.
4. **Sharding**: split data by key across DB instances.
5. **CQRS**: separate read and write models.

## Reliability Patterns
- **Circuit Breaker**: stop calling failing service, fail fast.
- **Retry with Backoff**: exponential delay between retries.
- **Bulkhead**: isolate failures to one pool/thread.
- **Rate Limiting**: token bucket, sliding window.
- **Idempotency Keys**: safe retries for payments/orders.

## Capacity Estimation (Quick)
```
1M DAU × 10 requests/day = 10M requests/day ≈ 115 req/s
Average response size: 50KB → 5 GB/day bandwidth
Storage: 1M users × 1KB/user profile = 1GB
         + 10M events/day × 500B = 5GB/day → 1.8TB/year
```

## Communication
- **Sync**: REST/gRPC — request-response, immediate consistency.
- **Async**: Kafka/RabbitMQ/SQS — decoupled, eventual consistency.
- **When async**: long-running tasks, third-party calls, fan-out, rate limiting downstream.

## Anti-patterns
- Microservices before product-market fit.
- No idempotency on payment endpoints.
- Cache without invalidation strategy.
- Single point of failure in critical path.
- Premature optimization — scale when you need to.