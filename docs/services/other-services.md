# 📡 Other Key Services

## Report Service

**Location:** `cosmicsec-services/services/report_service/`  
**Port:** 8005  
**Framework:** FastAPI

### Features
- **Multi-Format Export** — PDF, HTML, JSON, CSV, Excel
- **Template Engine** — Executive, Technical, Compliance templates
- **AI-Generated Summaries** — Executive summaries via AI
- **Scheduled Reports** — Automated periodic reports
- **Custom Branding** — Company logo, colors, disclaimers

### API Endpoints
```http
POST /api/v1/reports/generate    # Generate new report
GET  /api/v1/reports           # List reports
GET  /api/v1/reports/{id}      # Get report details
GET  /api/v1/reports/{id}/download  # Download report
DELETE /api/v1/reports/{id}     # Delete report
```

### Report Types
| Type | Description | Audience |
|------|-------------|----------|
| **Executive** | High-level summary, business risk | C-Level, Management |
| **Technical** | Detailed findings, technical details | Security Team, IT |
| **Compliance** | Regulatory compliance focus | Auditors, Compliance |
| **Vulnerability** | Vuln-centric view | Analysts, Pentesters |

---

## Collab Service (SOC Collaboration)

**Location:** `cosmicsec-services/services/collab_service/`  
**Port:** 8006  
**Framework:** FastAPI + WebSockets

### Features
- **Real-Time Whiteboard** — Collaborative drawing/diagramming
- **War Room** — Incident response collaboration
- **Live Chat** — Team communication
- **Shared Notes** — Collaborative documentation
- **Screen Sharing** — WebRTC-based sharing
- **Action Items** — Task assignment and tracking

### WebSocket Events
```json
{
  "event": "whiteboard_update",
  "user": "analyst_123",
  "data": {
    "action": "draw",
    "tool": "rectangle",
    "coords": [100, 200, 300, 400]
  }
}
```

---

## Bug Bounty Service

**Location:** `cosmicsec-services/services/bugbounty_service/`  
**Port:** 8007  
**Framework:** FastAPI

### Features
- **Program Management** — Create/manage bounty programs
- **Vulnerability Submission** — Researchers submit findings
- **Triage Workflow** — Validate, prioritize, reward
- **Payment Integration** — Automated payouts via Stripe
- **Leaderboard** — Top researchers ranking
- **Safe Harbor** — Legal protection for researchers

### API Endpoints
```http
POST /api/v1/bounty/programs          # Create program
GET  /api/v1/bounty/programs          # List programs
POST /api/v1/bounty/submissions       # Submit vulnerability
GET  /api/v1/bounty/submissions/{id} # Get submission
PUT  /api/v1/bounty/submissions/{id}/triage  # Triage
POST /api/v1/bounty/payouts           # Process payout
```

---

## DDoS Protection Service

**Location:** `cosmicsec-services/services/ddos_protection/`  
**Port:** 8021  
**Framework:** FastAPI

### Features
- **10K+ RPS Mitigation** — Handle massive attacks
- **Challenge-Response** — JavaScript/CAPTCHA challenges
- **Rate Limiting** — Per-IP/per-endpoint limits
- **Geofencing** — Block by country/region
- **Traffic Analysis** — Identify attack patterns
- **Auto-Mitigation** — Automatic rule generation

### Configuration Example
```json
{
  "protection_id": "protect_001",
  "target": "api.example.com",
  "rules": [
    {
      "type": "rate_limit",
      "threshold": 1000,
      "window": "1m",
      "action": "challenge"
    },
    {
      "type": "geo_block",
      "countries": ["CN", "RU"],
      "action": "block"
    }
  ]
}
```

---

## ZTNA (Zero-Trust Network Access)

**Location:** `cosmicsec-services/services/ztna/`  
**Port:** 8022  
**Framework:** FastAPI

### Features
- **Device Posture Check** — OS version, patches, antivirus
- **mTLS Authentication** — Mutual TLS for services
- **Micro-Segmentation** — Per-resource access control
- **Just-In-Time Access** — Temporary access grants
- **Session Recording** — Audit all ZTNA sessions
- **Integration** — SSO, MDM, EDR integration

### Access Policy Example
```json
{
  "policy_id": "pol_001",
  "name": "Developer Access to Staging",
  "resources": ["staging-api", "staging-db"],
  "conditions": {
    "user_role": "developer",
    "device_posture": {
      "os": "updated",
      "antivirus": "active"
    },
    "time_window": "9:00-17:00",
    "mfa_required": true
  }
}
```

---

## Threat Intel Feeds Service

**Location:** `cosmicsec-services/services/threat_intel_feeds/`  
**Port:** 8023  
**Framework:** FastAPI

### Features
- **7+ Intelligence Feeds** — STIX/TAXII, MISP, OTX, etc.
- **IoC Management** — Indicators of Compromise tracking
- **Feed Normalization** — Unified format across feeds
- **Automated Enrichment** — DNS, WHOIS, GeoIP
- **Threat Scoring** — Risk scoring for indicators
- **Export** — STIX 2.1, OpenIOC, CSV

### Supported Feeds
| Feed | Type | Update Frequency |
|------|------|-----------------|
| **STIX/TAXII** | Industry standard | Real-time |
| **MISP** | Threat sharing platform | Hourly |
| **OTX (AlienVault)** | Community intel | Daily |
| **VirusTotal** | File/URL analysis | Real-time |
| **Shodan** | Internet-connected devices | Daily |
| **CISA KEV** | Known exploited vulns | Daily |
| **FBI InfraGard** | US government intel | Weekly |

---

## Smart Contract Audit Service

**Location:** `cosmicsec-services/services/smart_contract_audit/`  
**Port:** 8024  
**Framework:** FastAPI

### Features
- **Multi-Language Support** — Solidity, Vyper, Go, Rust
- **Static Analysis** — Slither, Mythril, Oyente
- **Dynamic Analysis** — Fuzzing with Echidna
- **Manual Review** — Human expert review option
- **Compliance Checks** — ERC standards, security best practices
- **Gas Optimization** — Suggest gas-efficient patterns

### Supported Blockchains
- Ethereum (EVM)
- Binance Smart Chain
- Polygon
- Avalanche
- Solana (Rust)
- Cosmos (Go)

### Audit Report Example
```json
{
  "audit_id": "audit_12345",
  "contract": "Token.sol",
  "chain": "ethereum",
  "findings": [
    {
      "severity": "critical",
      "title": "Reentrancy Vulnerability",
      "description": "The withdraw function is vulnerable to reentrancy...",
      "line": 45,
      "recommendation": "Use Checks-Effects-Interactions pattern",
      "cve": null
    }
  ],
  "summary": {
    "critical": 1,
    "high": 2,
    "medium": 3,
    "low": 5,
    "gas_optimizations": 2
  }
}
```

---

## 3D Visualization Service

**Location:** `cosmicsec-services/services/3d_visualization/`  
**Port:** 8025  
**Framework:** FastAPI + Three.js

### Features
- **Network Topology** — Interactive node-link diagrams
- **Threat Heatmaps** — 3D visualization of threats
- **Real-Time Updates** — Live data streaming to 3D scene
- **Camera Controls** — Rotate, zoom, pan
- **Node Selection** — Click nodes for details
- **Export** — PNG, glTF (3D model)

### Frontend Integration
```typescript
import { ThreeJSVisualization } from '@/components/visualization/ThreeJSVisualization';

<ThreeJSVisualization
  nodes={[
    { id: 'server1', label: 'Web Server', type: 'server', severity: 'high' },
    { id: 'db1', label: 'Database', type: 'database', severity: 'medium' }
  ]}
  edges={[
    { source: 'server1', target: 'db1', protocol: 'mysql' }
  ]}
  onNodeClick={(node) => showNodeDetails(node)}
  onEdgeClick={(edge) => showConnectionDetails(edge)}
/>
```

---

## Breach Attack Simulation Service

**Location:** `cosmicsec-services/services/breach_attack_sim/`  
**Port:** 8026  
**Framework:** FastAPI

### Features
- **Automated Pen-Testing** — Simulate real attacks
- **MITRE ATT&CK Mapping** — Map simulated attacks to framework
- **Safe Simulation** — No actual damage to systems
- **Purple Team** — Red+Blue team collaboration
- **Scenario Library** — Pre-built attack scenarios
- **Report Generation** — Detailed simulation reports

### Attack Scenarios
| Scenario | MITRE Tactics | Duration |
|----------|----------------|----------|
| **Ransomware Simulation** | Initial Access, Lateral Movement, Impact | 2 hours |
| **Phishing Campaign** | Initial Access, Credential Access | 1 hour |
| **Insider Threat** | Collection, Exfiltration | 3 hours |
| **Supply Chain** | Initial Access, Persistence | 4 hours |

---

## Edge Computing Service

**Location:** `cosmicsec-services/services/edge_computing/`  
**Port:** 8027  
**Framework:** FastAPI

### Features
- **Edge Node Management** — Deploy/manage edge nodes
- **Fog Computing** — Hierarchical edge architecture
- **Latency Optimization** — Route to nearest edge
- **Offline Capability** — Edge continues when cloud disconnected
- **Load Balancing** — Distribute across edge nodes
- **Monitoring** — Node health, latency, throughput

### Edge Topology
```
Cloud (Central)
    ↓
Fog Nodes (Regional)
    ↓
Edge Nodes (Local - IoT devices, 5G base stations)
    ↓
Endpoints (Sensors, cameras, etc.)
```

---

## Next Steps

- [Complete Service List](./) (24 more services to document)
- [Service Communication Patterns](../architecture/service-discovery.md)
- [Docker Compose Configuration](../deploy/docker-compose.md)
- [Kubernetes Manifests](../deploy/kubernetes.md)
