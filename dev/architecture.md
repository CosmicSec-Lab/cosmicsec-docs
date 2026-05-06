# Architecture Guide#

**CosmicSec System Architecture.**

## Overview#

Understand the high-level architecture of CosmicSec platform.

## System Components#

```
┌─────────────────────────────────────────────────────────────┐
│                         User / SOC Analyst                        │
└──────────────────────────────┬──────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                   cosmicsec-web (Frontend)                    │
│         React 19 + TypeScript 5.5 + Glassmorphism            │
└──────────────────────────────┬──────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│              API Gateway (Port 8000) - Hybrid Router         │
│   REST + GraphQL | Circuit Breakers | Rate Limiting         │
└─────────────┬─────────────┬─────────────┬──────────────┘
              │             │             │             │
              ▼             ▼             ▼             ▼
    ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐
    │  Auth   │  │  Scan   │  │   AI    │  │ Recon   │
    │ Service │  │ Service │  │ Service │  │ Service │
    │  :8001  │  │  :8002  │  │  :8003  │  │  :8004  │
    └─────────┘  └─────────┘  └─────────┘  └─────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│    CosmicSec Core (RBAC, SSO, Quantum Crypto, etc.)    │
└──────────────────────────────┬──────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│         DeepIntel PRO (Port 8032) - Dark Web            │
│      17+ Dark Web Networks Monitored                    │
└─────────────────────────────────────────────────────────────┘
```

## Data Flow#

1. **User request** → Frontend → API Gateway#
2. **Gateway** routes to appropriate service#
3. **Service** processes request, interacts with Core#
4. **Core** handles RBAC, crypto, data residency#
5. **DeepIntel** provides threat intelligence#
6. **Response** flows back through Gateway to Frontend#

## Technology Stack#

- **Frontend**: React 19, TypeScript 5.5, Tailwind CSS#
- **Backend**: Python 3.11+, FastAPI, Uvicorn#
- **Database**: PostgreSQL 17, Redis 7, MongoDB 8#
- **Search**: Elasticsearch 8#
- **Container**: Docker, Kubernetes#
- **Message Queue**: NATS#
