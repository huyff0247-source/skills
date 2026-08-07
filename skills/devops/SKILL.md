---
name: devops
description: DevOps & infrastructure: Docker, Kubernetes, CI/CD, Terraform, monitoring. Use when deploying, containerizing, or setting up pipelines.
trigger: \bDocker\b ;; \bKubernetes\b ;; \bk8s\b ;; \bCI/CD\b ;; \bpipeline\b ;; \bTerraform\b ;; \bdeploy\b.*(code|app|server|container|image|docker|production|staging|pipeline|ứng dụng|hệ thống) ;; (code|app|server|container|image|docker).*\bdeploy\b ;; \bcontainerize\b ;; \bcontainer image\b ;; \bDockerfile\b ;; \bmonitoring\b ;; \bGrafana\b ;; \bPrometheus\b ;; \bnginx\b ;; \bproxy\b ;; \bDNS\b ;; \bSSL\b ;; \bcertificate\b ;; \btriển khai\b.*(ứng dụng|hệ thống|code|server|docker|container)
---

# DevOps & Infrastructure (Compact)

## Docker Best Practices
```dockerfile
# Multi-stage build — small final image
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER node
EXPOSE 3000
CMD ["node", "dist/server.js"]
```
- **Never run as root** — `USER node` or similar.
- **`.dockerignore`** — exclude `node_modules`, `.git`, build artifacts.
- **One process per container** — use orchestrator for sidecars.
- **Health checks**: `HEALTHCHECK --interval=30s CMD curl -f http://localhost:3000/health || exit 1`

## Kubernetes Essentials
```yaml
# Resource limits are NOT optional in production
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "500m"

# Probes
livenessProbe:   # restart if dead
  httpGet: { path: /health, port: 3000 }
readinessProbe:  # stop traffic if not ready
  httpGet: { path: /ready, port: 3000 }
```

## CI/CD Pipeline
```
Push → Lint → Test → Build → Security Scan → Deploy Staging → Smoke Test → Deploy Prod
```
- **Fail fast**: linters and unit tests first (seconds), integration later (minutes).
- **Artifact once**: build one Docker image, promote through environments.
- **Secrets**: never in code, never in Dockerfile. Use sealed secrets / vault / CI variables.
- **Rollback plan**: every deploy must be reversible in <5 minutes.

## Monitoring Stack
| Tool | Purpose |
|---|---|
| **Prometheus** | Metrics collection + alerting |
| **Grafana** | Dashboards |
| **Loki** | Log aggregation |
| **Tempo** | Distributed tracing |
| **Sentry** | Error tracking |

## Critical Alerts
- Error rate > 1% of requests
- P99 latency > 2× baseline
- Disk/memory > 85%
- Certificate expiry < 7 days
- Health check failing

## Anti-patterns
- Latest tag in production (`:latest` = surprise at 3am).
- No resource limits → noisy neighbor kills cluster.
- Manual deploys → irreproducible, no audit trail.
- Monitoring that only pages when users already complain.
- Secrets in environment variables (visible in `docker inspect`, child processes).