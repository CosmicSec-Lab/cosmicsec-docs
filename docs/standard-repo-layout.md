# CosmicSec — Standard Repository Layout

This layout is the **baseline structure** for all 9 CosmicSec repositories. Each repo stays independent but uses the same predictable organization to improve consistency, onboarding, and tooling.

```text
<repo-root>/
├── docs/               # Architecture, ADRs, API notes, runbooks
├── src/                # Application code (language-specific)
├── tests/              # Unit/integration tests
├── configs/            # Example configs, schemas, env templates
├── scripts/            # Automation and developer scripts
├── assets/             # Images, branding, demo assets
├── deploy/             # Docker, compose, Helm, k8s manifests
├── .gitignore
├── LICENSE
├── README.md
└── pyproject.toml / package.json / Cargo.toml
```

## Repo-Specific Notes

### cosmicsec-core
- `src/api_gateway/`
- `src/cosmicsec_platform/`
- `src/common/`
- `deploy/` holds Docker + compose + k8s manifests

### cosmicsec-services
- `src/services/` split by domain (auth, scan, report, etc.)
- `src/services/api_router/` becomes `src/services/gateway_api/`

### cosmicsec-ai
- `src/ai_service/`
- `src/ai_service/api/` → split into `routes/v1` and `routes/internal`

### cosmicsec-deepintel
- `src/deepintel/` (crawler + OSINT pipelines)

### cosmicsec-web
- `web/` → `src/` (React/Vite)
- `mobile/` stays under `apps/mobile/`

### cosmicsec-cli
- `src/cosmicsec_agent/`
- `packages/` remains for Homebrew/NPM/Rust

### cosmicsec-sdk
- `python/`, `typescript/` stay, plus `docs/` for API contracts

### cosmicsec-deploy
- `deploy/` stays, includes Terraform + Helm + k8s

### cosmicsec-docs
- `docs/` remains canonical architecture and diagrams
