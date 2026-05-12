# Services Documentation

## Overview

CosmicSec-Lab consists of **8 core modules** with specialized services for security operations.

---

## 1. cosmicsec-core (API Gateway)

**Path**: `cosmicsec-core/api_gateway/`

Central entry point providing:

- REST + GraphQL federation routing
- JWT validation + RBAC enforcement
- Circuit breaker (10 failures/60s)
- Rate limiting (100-2000 req/min)
- Service discovery with health checks

**Key Files**:
- `core/routers/` - REST endpoints
- `core/middleware/` - JWT, RBAC, CORS
- `core/circuit_breaker.py` - Resilience patterns
- `core/common/feature_flags.py` - Feature flags

---

## 2. cosmicsec-services (Backend Microservices)

**Path**: `cosmicsec-services/src/services/`

### Auth Service
- JWT + SSO (SAML/OIDC)
- TOTP 2FA with Fernet encryption
- RBAC (8 roles, 20+ permissions)
- Login rate limiter

### Scan Service
- SAST/DAST/SCA scanners
- 17+ tool integration (nmap, nuclei, nikto, etc.)
- Async scan execution
- Multi-target scanning

### AI Service (Helix)
- Multi-model LLM (GPT-4o, Claude 3, Llama, Ollama)
- LangGraph autonomous agents
- DefensiveAI auto-remediation
- ZeroDayPredictor ML engine

### Recon Service
- OSINT enrichment
- Subdomain enumeration
- DNS recon, DNS-over-TLS
- GreyNoise, DNS dumpster

### Collab Service
- WebSocket real-time
- Redis Pub/Sub scaling
- Chat, threat hunting collab

### SOC Service
- Dashboard analytics
- Threat hunting workbench
- MITRE ATT&CK mapping
- Incident management

---

## 3. cosmicsec-ai (Helix AI Engine)

**Path**: `cosmicsec-ai/src/ai_service/`

### Multi-Provider LLM
```python
class LLMProvider:
    async def chat(messages: list[ChatMessage]) -> ChatResponse
# OpenAI, Anthropic, Ollama, Fallback chain
```

### Autonomous Agents
```python
class AgentType:
    TRIAGE = "triage"
    ANALYSIS = "analysis"
    CORRELATION = "correlation"
    REMEDIATION = "remediation"
```

### LangGraph Pipeline
```
recon_node → scan_node → analyze_node → report_node
```

### Analysis Modules
- `analysis/zero_day.py` - ML threat prediction
- `intelligence/defensive.py` - DefensiveAI
- `intelligence/copilot.py` - Security copilot

---

## 4. cosmicsec-deepintel (DeepIntel PRO)

**Path**: `cosmicsec-deepintel/src/deepintel/`

### Dark Web Networks
- Tor, I2P, IPFS, ZeroNet, Freenet
- DNS-over-TLS, CNAME enumeration
- Blockchain tracers

### Intelligence Services
- `intelligence/threat_hunting.py` - Hunt engine
- `intelligence/ransomware_tracker.py` - Group monitoring
- `monitors/honeypot.py` - Cowrie, Dionaea

### STIX/TAXII Client
```python
class TAXIIClient:
    async def query_stix_objects(bundle: STIXBundle) -> list[STIXObject]
    async def push_threat_indicators(indicators: list[Indicator]) -> None
```

---

## 5. cosmicsec-web (React Frontend)

**Path**: `cosmicsec-web/apps/web/src/`

### Tech Stack
- React 19 + TypeScript 5.5
- Next.js App Router
- Zustand state management
- shadcn/ui components
- Three.js 3D visualization

### Key Pages
- `pages/login.tsx` - Auth
- `pages/dashboard.tsx` - SOC dashboard
- `pages/scans.tsx` - Scan management
- `pages/recon.tsx` - Reconnaissance
- `pages/ai_analysis.tsx` - AI copilot

### Contexts
- `auth_context.tsx` - Authentication state
- `theme_context.tsx` - Glassmorphism theming
- `notification_context.tsx` - Real-time alerts

---

## 6. cosmicsec-cli (CLI Agent)

**Path**: `cosmicsec-cli/src/cosmicsec_agent/`

### Hybrid Engine
```python
class HybridEngine:
    plan(instruction: str) -> ExecutionPlan
    execute(plan: ExecutionPlan) -> StepResult
```

### Tool Parsers
```python
parsers/
├── nmap_parser.py      # Nmap XML output
├── nuclei_parser.py    # Nuclei JSON
├── ffuf_parser.py      # FFuf JSON
├── burpsuite_parser.py # Burpsuite XML
└── ... 17+ total
```

### Execution Modes
- `static_execution_mode` - Deterministic
- `dynamic_execution_mode` - AI-assisted
- `local_execution_mode` - CLI only

---

## 7. cosmicsec-sdk

**Path**: `cosmicsec-sdk/`

### Python SDK
```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="...", base_url="...")
scans = await client.scans.list()
```

### TypeScript SDK
```typescript
import { CosmicSecClient } from '@cosmicsec/sdk'

const client = new CosmicSecClient({ apiKey: '...' })
const scans = await client.scans.list()
```

---

## 8. cosmicsec-deploy

**Path**: `cosmicsec-deploy/`

### Docker Compose
```yaml
services:
  api_gateway: ...
  auth_service: ...
  scan_service: ...
  ai_service: ...
  deepintel: ...
```

### Kubernetes
- Helm charts per service
- HPA (Horizontal Pod Autoscaler)
- Service mesh (Istio-compatible)

### CI/CD
- GitHub Actions workflows
- ArgoCD for GitOps

---

## Related Links

- [API Gateway](../architecture/gateway.md)
- [Security & RBAC](../security/)
- [Deployment Guide](../deployment/)