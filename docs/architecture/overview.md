# Architecture Overview

## System Design

CosmicSec-Lab is an **enterprise security operations platform** with 9 core modules, built on microservices architecture.

## Core Modules

```
┌─────────────────────────────────────────────────────────────────────┐
│                         User / SOC Analyst                          │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   cosmicsec-web (Frontend)                          │
│         React 19 + TypeScript + Glassmorphism UI                    │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│              cosmicsec-core (API Gateway)                            │
│         FastAPI + GraphQL Federation + mTLS                          │
└─────────────┬──────────────┬──────────────┬──────────────┬─────────┘
              │              │              │              │
              ▼              ▼              ▼              ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│  Auth Service   │ │  Scan Service   │ │    AI Service   │ │  Recon Service  │
│ (JWT, RBAC)    │ │ (SAST/DAST)    │ │  (Helix/LLM)   │ │   (OSINT)      │
└─────────────────┘ └─────────────────┘ └─────────────────┘ └─────────────────┘
```

## Module Breakdown

### 1. cosmicsec-core
- **API Gateway**: FastAPI + GraphQL federation
- **Circuit Breaker**: 10 failures / 60s threshold
- **Rate Limiting**: 100-2000 req/min per service
- **RBAC System**: 8 roles, 20+ permissions
- **Common Utilities**: Feature flags, rate limiters, circuit breakers

### 2. cosmicsec-services
Backend microservices:
- **Auth Service**: JWT + SSO (SAML/OIDC) + TOTP 2FA
- **Scan Service**: SAST/DAST/SCA, integration with 17+ tools
- **AI Service (Helix)**: Multi-model LLM + LangGraph agents
- **Recon Service**: OSINT, subdomain enumeration, DNS recon
- **Collab Service**: WebSocket real-time collaboration
- **SOC Service**: Dashboard, threat hunting, MITRE ATT&CK

### 3. cosmicsec-ai (Helix AI Engine)
- **Multi-Model LLM**: GPT-4o, Claude 3 Opus, Llama 3.1, Ollama
- **Autonomous Agents**: Triage, analysis, correlation, remediation
- **LangGraph Pipeline**: Recon → Scan → Analyze → Report
- **DefensiveAI**: Auto-remediation engine
- **ZeroDayPredictor**: ML-based threat prediction

### 4. cosmicsec-deepintel (DeepIntel PRO)
- **Dark Web Networks**: Tor, I2P, IPFS, ZeroNet, Freenet
- **Ransomware Tracker**: Group monitoring, victim tracking
- **STIX/TAXII Client**: Threat intelligence feeds
- **Honeypot Monitors**: Cowrie, Dionaea integration
- **Messaging Crawlers**: Signal, Telegram, WhatsApp

### 5. cosmicsec-web
- **React 19** + TypeScript + Next.js
- **Glassmorphism UI**: Premium design system
- **Zustand State**: Centralized state management
- **WebSocket**: Real-time collaboration
- **3D Visualization**: Three.js attack path

### 6. cosmicsec-cli
- **Hybrid Engine**: AI planner + deterministic executor
- **17+ Tool Parsers**: Burpsuite, ffuf, gobuster, nmap, nuclei, etc.
- **Interactive Mode**: Natural language security ops
- **Autonomous Mode**: Local agent execution

### 7. cosmicsec-sdk
- **Python SDK**: Pydantic models, async client
- **TypeScript SDK**: Zod validation, WebSocket client
- **Examples**: Scan creation, findings retrieval, agent invocation

### 8. cosmicsec-deploy
- **Docker Compose**: Local development
- **Kubernetes**: Multi-zone production
- **CI/CD**: GitHub Actions, ArgoCD
- **Observability**: Prometheus, Grafana, Jaeger

### 9. cosmicsec-docs
- **Architecture Diagrams**: System overview, data flow
- **API References**: REST, GraphQL, WebSocket
- **Service Documentation**: Auth, Scan, AI, Recon, Collab
- **Security Guides**: RBAC, SSO, Zero-Trust, Quantum-ready
- **Deployment Guides**: Docker, Kubernetes, CI/CD

## Data Architecture

| Layer | Technology | Purpose |
|-------|------------|---------|
| **API Gateway** | FastAPI, GraphQL | Routing, auth, rate limiting |
| **Services** | Python, FastAPI | Business logic |
| **AI Engine** | LangChain, LangGraph | Autonomous operations |
| **Data Stores** | PostgreSQL, Redis, MongoDB, Elasticsearch, Kafka | Primary DB, cache, search, events |
| **Frontend** | React, TypeScript | User interface |

## Security Architecture

### Zero-Trust Model
- Never trust, always verify
- mTLS between services
- Continuous authentication
- Device posture checks

### Authentication
```
User → API Gateway → Auth Service → JWT Token → Subsequent Requests
```

### RBAC Roles
1. super_admin
2. admin
3. analyst
4. auditor
5. viewer
6. api_user
7. soc_analyst
8. threat_hunter

### Quantum-Ready Cryptography
- **Kyber (ML-KEM)**: Key encapsulation
- **Dilithium (ML-DSA)**: Digital signatures
- Hybrid classical + post-quantum schemes

## Key Features

| Feature | Module | Description |
|---------|--------|-------------|
| **Autonomous Agents** | ai-service | LangGraph-powered triage/analysis/correlation |
| **Threat Hunting** | soc-service | Real-time dashboard, MITRE ATT&CK |
| **Vulnerability Scanning** | scan-service | SAST/DAST/SCA, 17+ tool integration |
| **Dark Web Intelligence** | deepintel | Tor/I2P/IPFS, ransomware tracking |
| **Multi-Level Caching** | core | L1 (in-memory) + L2 (Redis) |
| **3D Attack Path** | web | Three.js visualization |
| **Circuit Breakers** | core | Resilience patterns |

## Communication Patterns

| Pattern | Protocol | Use Case |
|---------|----------|----------|
| **REST APIs** | HTTP/JSON | CRUD, queries |
| **GraphQL** | GraphQL | Dashboard, aggregations |
| **gRPC** | Protocol Buffers | Ingest service (Rust ↔ Python) |
| **WebSockets** | WebSocket | Real-time collab, live scans |
| **Kafka** | Event Streaming | Async events, audit logs |

## Resource Links

- [API Gateway](./gateway.md)
- [Services Documentation](../services/)
- [Security & RBAC](../security/)
- [Deployment](../deployment/)