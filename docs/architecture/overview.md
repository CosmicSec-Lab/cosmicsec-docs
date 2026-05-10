# 🏗️ Architecture Overview

## System Architecture

CosmicSec uses a **microservices architecture** with 25+ independent services communicating via REST APIs, GraphQL Federation, gRPC, and WebSockets.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         User / SOC Analyst                          │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   cosmicsec-web (Frontend)                          │
│         React 19 + TypeScript 5.5 + Glassmorphism UI                │
│         Port: 3000                                                  │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│              API Gateway (Port 8000) - Hybrid Router                │
│   • REST + GraphQL Federation                                       │
│   • Circuit Breakers (10 failures/60s)                              │
│   • Rate Limiting (100-2000 req/min per service)                    │
│   • Service Discovery                                               │
│   • Request Routing & Load Balancing                                │
└─────────────┬──────────────┬──────────────┬──────────────┬─────────┘
              │              │              │              │
              ▼              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │ Auth Service│ │ Scan Service│ │  AI Service │ │Recon Service│
    │  Port: 8001 │ │  Port: 8002 │ │  Port: 8003 │ │  Port: 8004 │
    └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
              │              │              │              │
              ▼              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │Report Service│ │Collab Service│ │BugBounty Svc│ │Phase5/OpHub │
    │  Port: 8005 │ │  Port: 8006 │ │  Port: 8007 │ │  Port: 8010 │
    └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
              │              │              │              │
              ▼              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │IoT/OT Sec   │ │DDoS Protect │ │    ZTNA     │ │Threat Intel │
    │  Port: 8020 │ │  Port: 8021 │ │  Port: 8022 │ │  Port: 8023 │
    └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
              │              │              │              │
              ▼              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │Smart Contract│ │3D Visualize│ │Breach Sim   │ │Edge Computing│
    │  Port: 8024 │ │  Port: 8025 │ │  Port: 8026 │ │  Port: 8027 │
    └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
              │              │              │              │
              ▼              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │ SLA Manager │ │Theme Builder│ │Onboard Wizrd│ │ NLP Search  │
    │  Port: 8028 │ │  Port: 8029 │ │  Port: 8030 │ │  Port: 8031 │
    └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
              │              │              │
              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │Notification │ │Compliance   │ │  Org Service│
    │  Port: 8011 │ │  Port: 8012 │ │  Port: 8013 │
    └─────────────┘ └─────────────┘ └─────────────┘
              │              │              │
              ▼              ▼              ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │Admin Service│ │Egress Service│ │Ingest Service│
    │  Port: 8014 │ │  Port: 8015 │ │  Port: 8016 │
    └─────────────┘ └─────────────┘ └─────────────┘
                               │
                               ▼
        ┌──────────────────────────────────────────┐
        │     DeepIntel PRO (Port 8032)          │
        │  17+ Dark Web Networks Monitored        │
        │  • Tor, I2P, IPFS, ZeroNet, Freenet   │
        │  • GNUnet, Lokinet, Yggdrasil, etc.    │
        └──────────────────────────────────────────┘
                               │
                               ▼
        ┌──────────────────────────────────────────┐
        │          Data Layer                       │
        │  • PostgreSQL (Primary DB)               │
        │  • Redis (Cache + Pub/Sub)               │
        │  • Elasticsearch (Search + Logs)         │
        │  • MinIO (Object Storage)                │
        │  • Kafka (Event Streaming)               │
        └──────────────────────────────────────────┘
```

## Core Components

### 1. Frontend (cosmicsec-web)
- **Framework:** React 19 with TypeScript 5.5
- **UI Design:** Glassmorphism with premium components
- **State Management:** React Context + Hooks
- **Routing:** React Router v7
- **Charts:** Recharts + Three.js for 3D visualization
- **Real-Time:** WebSocket client for live updates
- **PWA:** Progressive Web App with offline support
- **Responsive:** 320px → 4K display support

### 2. API Gateway (cosmicsec-core)
- **Framework:** FastAPI with hybrid REST + GraphQL
- **GraphQL:** Strawberry GraphQL federation
- **Service Discovery:** Dynamic service registry
- **Circuit Breaker:** Prevents cascade failures
- **Rate Limiting:** Per-service configurable limits
- **Authentication:** JWT validation + RBAC enforcement
- **Metrics:** Prometheus integration
- **Health Checks:** `/health` endpoint on all services

### 3. Microservices (25+)
Each service is:
- **Independent:** Own codebase, dependencies, database
- **Containerized:** Docker + Kubernetes ready
- **Scalable:** Horizontal scaling via K8s
- **Resilient:** Health checks, circuit breakers, retries
- **Observable:** Metrics, logs, tracing

### 4. Data Layer
- **PostgreSQL:** Primary relational data
- **Redis:** Caching + Pub/Sub for real-time
- **Elasticsearch:** Full-text search + log aggregation
- **MinIO:** S3-compatible object storage
- **Kafka:** Event streaming for async processing

## Communication Patterns

### REST APIs
- Synchronous request/response
- JSON payloads
- OpenAPI/Swagger documentation
- Used for: CRUD operations, queries

### GraphQL Federation
- Unified schema across services
- Single endpoint for complex queries
- Reduces over-fetching
- Used for: Dashboard data, aggregations

### gRPC
- High-performance binary protocol
- Used for: Ingest service (Rust ↔ Python)
- Protocol buffers for schema definition

### WebSockets
- Bidirectional real-time communication
- Used for: Collaborative SOC, live scan updates
- Redis Pub/Sub for scaling

### Event Streaming (Kafka)
- Async event propagation
- Used for: Scan results, notifications, audit logs
- Topic-based pub/sub

## Security Architecture

### Zero-Trust Model
- **Never trust, always verify**
- mTLS between services
- Device posture checks
- Continuous authentication

### Authentication Flow
```
User → API Gateway → Auth Service → JWT Token → Subsequent Requests
```

### RBAC System
- 8 Roles: super_admin, admin, analyst, auditor, viewer, api_user, soc_analyst, threat_hunter
- 20+ Permissions: scan:create, report:read, ai:use, etc.
- SSO Integration: SAML 2.0, OIDC, OAuth2

### Quantum-Ready Cryptography
- **Kyber (ML-KEM)** for key encapsulation
- **Dilithium (ML-DSA)** for digital signatures
- Hybrid classical + post-quantum schemes

## Scalability

### Horizontal Scaling
- Kubernetes Deployments with replica counts
- HPA (Horizontal Pod Autoscaler) based on CPU/memory
- Stateless services for easy scaling

### Caching Strategy
- Redis for API response caching
- CDN for static assets
- Browser caching for frontend

### Database Scaling
- Read replicas for PostgreSQL
- Sharding for large datasets
- Connection pooling

## Monitoring & Observability

### Metrics (Prometheus)
- Request rate, error rate, duration
- Business metrics (scans, vulnerabilities)
- System metrics (CPU, memory, disk)

### Logging (ELK Stack)
- Centralized log aggregation
- Structured JSON logging
- Log retention policies

### Tracing (OpenTelemetry)
- Distributed tracing across services
- Jaeger for trace visualization
- Performance bottleneck identification

### Dashboards (Grafana)
- 5+ pre-built dashboards
- Service health overview
- Security metrics
- SLA compliance

## Deployment Architecture

### Development
- Docker Compose with hot-reload
- Local services with volume mounts
- Mock external services

### Staging
- Kubernetes cluster (minimal)
- Production-like environment
- Integration testing

### Production
- Multi-zone Kubernetes cluster
- High availability (3+ replicas)
- Automated backups
- Disaster recovery

## Technology Stack Summary

| Layer | Technology |
|-------|------------|
| **Frontend** | React 19, TypeScript 5.5, Vite, Tailwind CSS |
| **Backend** | Python 3.11+, FastAPI, Uvicorn |
| **AI** | OpenAI, Anthropic, LangChain, Ollama |
| **Databases** | PostgreSQL, Redis, Elasticsearch, MinIO |
| **Message Queue** | Kafka, Redis Pub/Sub |
| **Container** | Docker, Kubernetes, Helm |
| **CI/CD** | GitHub Actions, ArgoCD |
| **Monitoring** | Prometheus, Grafana, Jaeger |
| **Security** | JWT, mTLS, RBAC, SSO |
| **Infrastructure** | Terraform, Pulumi, AWS/GCP/Azure |

## Design Principles

1. **Microservices First** — Independent deployability
2. **API-First** — All functionality exposed via APIs
3. **Security by Design** — Zero-trust, encryption everywhere
4. **Observability** — Metrics, logs, traces for everything
5. **Scalability** — Horizontal scaling, stateless services
6. **Resilience** — Circuit breakers, retries, graceful degradation
7. **Developer Experience** — Great docs, SDKs, CLI tools
8. **User Experience** — Premium UI, responsive, accessible

## Next Steps

- [API Gateway Documentation](./gateway.md)
- [Service Discovery](./service-discovery.md)
- [RBAC & SSO System](./../security/rbac-sso.md)
- [Data Residency & GDPR](./../security/data-residency.md)
- [Quantum Cryptography](./../security/quantum-crypto.md)
