# Key Modules Reference

## God Nodes (Most Connected Components)

These are the **architectural pillars** of CosmicSec-Lab with the highest connectivity:

### 1. _build_service_url() (109 edges)
**Module**: `cosmicsec-core/api_gateway/core_deps.py`

Central URL builder for inter-service communication:
```python
def _build_service_url(service: str, path: str = "") -> str:
    """Build service URL with proper routing"""
    base = _service_registry.get(service)
    return f"{base}/{path}"
```

**Related Services**:
- Auth Service → API Gateway
- Scan Service → API Gateway
- AI Service → API Gateway
- Recon Service → API Gateway

---

### 2. DefensiveAI (64 edges)
**Module**: `cosmicsec-ai/src/ai_service/intelligence/defensive.py`

AI-powered defensive security and auto-remediation engine:
```python
class DefensiveAI:
    async def generate_remediation(vuln: Finding) -> RemediationPlan
    async def can_auto_remediate(vuln: Finding) -> bool
    async def estimate_effort(vuln: Finding) -> EffortLevel
```

**Capabilities**:
- Automated vulnerability remediation
- Severity-based priority calculation
- Security hardening recommendations

---

### 3. FeatureFlagManager (59 edges)
**Module**: `cosmicsec-core/cosmicsec_platform/common/feature_flags.py`

Enterprise feature flag system with targeting:
```python
class FeatureFlagManager:
    async def is_enabled(flag: str, user: User) -> bool
    async def get_variant(flag: str, user: User) -> str
```

**Features**:
- Per-user targeting
- Per-org configuration
- Gradual rollouts

---

### 4. LoginRateLimiter (48 edges)
**Module**: `cosmicsec-core/cosmicsec_platform/common/rate_limiting.py`

Security-focused rate limiting for authentication:
```python
class LoginRateLimiter:
    MAX_ATTEMPTS = 5
    LOCKOUT_DURATION = 15  # minutes
    progressive_delay = True
```

---

### 5. OllamaProvider (46 edges)
**Module**: `cosmicsec-ai/src/ai_service/llm/providers.py`

Multi-provider LLM abstraction with Ollama support:
```python
class OllamaProvider(BaseLLMProvider):
    base_url: str = "http://localhost:11434"
    available_models: list[str] = ["llama3.1", "mistral"]

    async def chat(messages: list[ChatMessage]) -> ChatResponse
    async def list_models() -> list[Model]
```

---

### 6. ZeroDayPredictor (45 edges)
**Module**: `cosmicsec-ai/src/ai_service/analysis/zero_day.py`

ML-based zero-day threat prediction:
```python
class ZeroDayPredictor:
    async def predict_threat(cve_id: str, indicators: list[IOC]) -> ThreatScore
    async def train_on_historical(scans: list[Scan]) -> None
    async def get_anomaly_score(target: str) -> float
```

---

### 7. ThreatHuntingEngine (44 edges)
**Module**: `cosmicsec-deepintel/src/deepintel/intelligence/threat_hunting.py`

Real-time threat hunting across multiple data sources:
```python
class ThreatHuntingEngine:
    async def hunt_ioc(ioc: IOC) -> list[HuntResult]
    async def correlate_findings(findings: list[Finding]) -> list[Correlation]
    async def map_to_attack(finding: Finding) -> list[MITRETechnique]
```

---

### 8. OpenAIProvider (43 edges)
**Module**: `cosmicsec-ai/src/ai_service/llm/providers.py`

OpenAI GPT integration:
```python
class OpenAIProvider(BaseLLMProvider):
    model: str = "gpt-4o"
    api_key: str

    async def chat(messages: list[ChatMessage]) -> ChatResponse
```

---

### 9. UserModel (43 edges)
**Module**: `cosmicsec-core/cosmicsec_platform/common/models.py`

Core user model with RBAC support:
```python
class UserModel(BaseModel):
    id: str
    email: str
    roles: list[Role]
    org_id: str
    mfa_enabled: bool
    sso_provider: Optional[str]
```

---

### 10. TestDataMasking (42 edges)
**Module**: `cosmicsec-core/tests/test_premium_modules.py`

Security data masking for PII:
```python
class TestDataMasking:
    async def mask_finding_data(findings: list[Finding]) -> list[Finding]
    async def mask_scan_config(config: ScanConfig) -> ScanConfig
```

---

## Key Hyperedges (Group Relationships)

### Autonomous Agent Ecosystem
```
run_autonomous_agent ←→ _build_langchain_agent ←→ _deterministic_pipeline
     ↓                      ↓                        ↓
SecurityAgent ←→ AgentManager ←→ execute_autonomous_agent
```

### Multi-Agent Assessment Pipeline
```
triage_agent → analysis_agent → correlation_agent → remediation_agent
```

### Parser Ecosystem
```
burpsuite_parser, ffuf_parser, gobuster_parser, hashcat_parser,
hydra_parser, masscan_parser, metasploit_parser, nikto_parser,
nmap_parser, nuclei_parser, sqlmap_parser, wpscan_parser, zaproxy_parser
```

### Core API Gateway Module Structure
```
api_gateway_main ←→ core_deps ←→ graphql_runtime ←→ ingest_bridge
```

---

## Module Relationships

```
┌─────────────────────────────────────────────────────────────────┐
│                    cosmicsec-core (API Gateway)                  │
│  _build_service_url() ←→ FeatureFlagManager ←→ CircuitBreaker    │
└───────────────────────────┬─────────────────────────────────────┘
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│cosmicsec-ai    │ │cosmicsec-svc   │ │cosmicsec-deep  │
│  Helix AI      │ │  Services      │ │  Intel PRO     │
└────────┬───────┘ └────────┬───────┘ └────────┬───────┘
         │                  │                  │
         ▼                  ▼                  ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│DefensiveAI     │ │LoginRateLimiter│ │ThreatHunting   │
│ZeroDayPredictor│ │UserModel       │ │Engine          │
│OllamaProvider  │ │ScanService     │ │ZeroDayPredictor│
│OpenAIProvider  │ │AuthService     │ │RansomwareTracker│
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

---

## Security Flow

```
User Request
    ↓
API Gateway (JWT Validation + RBAC)
    ↓
LoginRateLimiter (brute-force protection)
    ↓
Service (e.g., Scan Service)
    ↓
FeatureFlagManager (per-org feature toggles)
    ↓
Response
```

---

## AI Engine Flow

```
User Query → API Gateway
    ↓
AI Service (Helix)
    ↓
┌────────────────────────────────────────────┐
│          LLM Provider Chain                │
│  OpenAIProvider → ClaudeProvider → Ollama  │
│           ↓ (fallback on failure)          │
└────────────────────────────────────────────┘
    ↓
LangGraph Agent (DefensiveAI / ZeroDayPredictor)
    ↓
Response (remediation, prediction, analysis)
```