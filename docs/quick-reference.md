# Quick Reference

## Project Structure

```
CosmicSec-Lab/
├── cosmicsec-core/        # API Gateway, common libs, RBAC
├── cosmicsec-services/    # Backend microservices
├── cosmicsec-ai/          # Helix AI Engine
├── cosmicsec-deepintel/   # DeepIntel PRO (Dark web OSINT)
├── cosmicsec-web/         # React Frontend
├── cosmicsec-cli/         # CLI Agent
├── cosmicsec-sdk/         # Python & TypeScript SDK
├── cosmicsec-deploy/      # Docker/K8s configs
└── cosmicsec-docs/        # This documentation
```

## Service Ports

| Service | Port | Protocol |
|---------|------|----------|
| API Gateway | 8000 | REST, GraphQL |
| Auth Service | 8001 | REST |
| Scan Service | 8002 | REST |
| AI Service (Helix) | 8003 | REST, WebSocket |
| Recon Service | 8004 | REST |
| Collab Service | 8006 | WebSocket |
| DeepIntel PRO | 8032 | REST |

## Key Commands

```bash
# Start all services
docker-compose up -d

# CLI agent
cosmicsec --help
cosmicsec scan run --target example.com
cosmicsec recon osint --domain example.com

# Python SDK
from cosmicsec import CosmicSecClient
client = CosmicSecClient(api_key="...")

# Health check
curl http://localhost:8000/health
```

## RBAC Roles

| Role | Permissions |
|------|-------------|
| `super_admin` | Full system |
| `admin` | User management |
| `analyst` | Scan management |
| `auditor` | Read-only audit |
| `viewer` | Read-only basic |
| `api_user` | API only |
| `soc_analyst` | SOC dashboard |
| `threat_hunter` | Advanced hunting |

## Security Features

- JWT + SSO (SAML/OIDC)
- TOTP 2FA
- RBAC (8 roles, 20+ permissions)
- mTLS between services
- Quantum-ready crypto (Kyber/Dilithium)
- Multi-level caching (L1 + L2)

## AI Models

| Provider | Model | Use Case |
|----------|-------|----------|
| OpenAI | GPT-4o | Primary AI |
| Anthropic | Claude 3 Opus | Analysis |
| Ollama | Llama 3.1 | Local/self-hosted |
| Fallback | Chain | Reliability |

## Quick Links

- [Architecture Overview](./architecture/overview.md)
- [API Gateway](./architecture/gateway.md)
- [Services Overview](./services/overview.md)
- [Security & RBAC](./security/overview.md)
- [Deployment](./deployment/overview.md)
- [Modules Reference](./architecture/modules.md)