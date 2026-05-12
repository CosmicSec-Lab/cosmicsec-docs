# CosmicSec-Lab

**Enterprise Security Operations Platform** - AI-powered, full-stack security suite with autonomous threat hunting, vulnerability scanning, and real-time collaboration.

```
CosmicSec-Lab/
├── cosmicsec-core/        # API Gateway, common libs
├── cosmicsec-services/    # Backend microservices
├── cosmicsec-ai/          # Helix AI Engine
├── cosmicsec-deepintel/   # DeepIntel PRO (Dark web OSINT)
├── cosmicsec-web/         # React Frontend
├── cosmicsec-cli/         # CLI Agent
├── cosmicsec-sdk/         # Python & TypeScript SDK
├── cosmicsec-deploy/      # Docker/K8s configs
└── cosmicsec-docs/        # Documentation
```

## Platform Overview

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Frontend** | React 19, TypeScript, Next.js | Glassmorphism UI, real-time dashboards |
| **Backend** | FastAPI, Python 3.11+ | Microservices, REST/GraphQL |
| **AI Engine** | LangChain, LangGraph, Multi-model LLM | Autonomous agents, threat analysis |
| **Dark Web OSINT** | Tor/I2P/IPFS networks | DarkNet monitoring, ransomware tracking |
| **Data Stores** | PostgreSQL, Redis, MongoDB, Elasticsearch, Kafka | Primary DB, caching, search, events |
| **CLI Agent** | Hybrid AI Engine | Natural language security operations |

## 9 Core Modules

### 1. cosmicsec-core
API Gateway with FastAPI, GraphQL federation, circuit breakers, rate limiting, JWT auth, RBAC system.

### 2. cosmicsec-services
Backend microservices: Auth, Scan, AI, Recon, Collab, Bug Bounty, SOC, Relay services.

### 3. cosmicsec-ai (Helix)
AI engine with multi-model LLM (OpenAI, Anthropic, Ollama), LangGraph agents, autonomous security operations.

### 4. cosmicsec-deepintel (DeepIntel PRO)
Dark web intelligence: Tor/I2P/IPFS monitoring, ransomware tracker, honeypot monitors, STIX/TAXII client.

### 5. cosmicsec-web
React frontend with Next.js, Zustand state, WebSocket real-time collaboration, 3D attack path visualization.

### 6. cosmicsec-cli
CLI agent with hybrid execution engine, natural language security operations, 17+ security tool parsers.

### 7. cosmicsec-sdk
Python & TypeScript SDK libraries with Pydantic/Zod validation for easy integration.

### 8. cosmicsec-deploy
Docker Compose, Kubernetes manifests, CI/CD with GitHub Actions, multi-zone deployment.

### 9. cosmicsec-docs
Complete project documentation with architecture diagrams, API references, and deployment guides.

## Quick Start

```bash
# Clone and setup
git clone https://github.com/your-org/cosmicsec-lab.git
cd cosmicsec-lab

# Start with Docker Compose
docker-compose up -d

# Or use CLI
pip install cosmicsec-cli
cosmicsec --help
```

## Key Features

| Feature | Description |
|---------|-------------|
| **Autonomous Agents** | LangGraph-powered agents for triage, analysis, correlation, remediation |
| **Threat Hunting** | Real-time SOC dashboard, MITRE ATT&CK mapping, anomaly detection |
| **Vulnerability Scanning** | SAST/DAST/SCA, integration with 17+ security tools |
| **Dark Web Intelligence** | Tor/I2P/IPFS networks, ransomware tracking, threat actor attribution |
| **Zero-Trust Security** | mTLS, continuous auth, RBAC, SSO (SAML/OIDC) |
| **Quantum-Ready Crypto** | Kyber/Dilithium post-quantum algorithms |
| **Multi-Level Caching** | L1 (in-memory) + L2 (Redis) tiered caching |
| **3D Visualization** | Three.js attack path visualization |

## Architecture Highlights

- **API Gateway**: REST + GraphQL federation, circuit breaker (10 failures/60s), rate limiting
- **Data Layer**: PostgreSQL (primary), Redis (cache), Elasticsearch (search), Kafka (events)
- **Security**: Zero-trust model, mTLS, RBAC (8 roles, 20+ permissions)
- **AI**: Multi-model ensemble (GPT-4o, Claude 3 Opus, Llama 3.1, Ollama)

## Resource Links

- [Architecture Overview](./docs/architecture/overview.md)
- [API Gateway](./docs/architecture/gateway.md)
- [Services Documentation](./docs/services/)
- [Security & RBAC](./docs/security/)
- [Deployment Guide](./docs/deployment/)

## License

Proprietary - All rights reserved