# 📜 Compliance Service#

## Overview#

The **Compliance Service** delivers enterprise-grade regulatory compliance automation with AI-powered gap analysis, automated evidence collection, and premium certification readiness dashboards.

**Location:** `cosmicsec-services/services/compliance_service/`  
**Port:** 8012  
**Framework:** FastAPI + OPA (Open Policy Agent)#

## Premium Features#

### 1. Compliance Standards (15+ Supported)
| Standard | Scope | AI Readiness Check |
|----------|-------|---------------------|
| **GDPR** | EU Data Protection | ✅ |
| **HIPAA** | US Healthcare | ✅ |
| **PCI-DSS** | Payment Cards | ✅ |
| **SOC 2 Type II** | Service Organizations | ✅ |
| **ISO 27001** | Information Security | ✅ |
| **NIST CSF** | Cybersecurity Framework | ✅ |
| **CCPA** | California Privacy | ✅ |
| **LGPD** | Brazil Privacy | ✅ |
| **PIPEDA** | Canada Privacy | ✅ |
| **FedRAMP** | US Federal | ✅ |
| **TISAX** | Automotive Security | ✅ |
| **Cyber Essentials** | UK Baseline | ✅ |
| **CIS Controls** | Critical Security | ✅ |
| **NERC CIP** | Energy Sector | ✅ |
| **IEC 62443** | Industrial Security | ✅ |

### 2. AI-Powered Gap Analysis
- **Automated Scanning** — Continuous compliance monitoring
- **Gap Identification** — AI finds missing controls
- **Remediation Roadmap** — Prioritized action plan
- **Evidence Collection** — Auto-collect proof
- **Readiness Score** — 0-100% compliance score#

### 3. Executive Dashboards
- **Real-Time Compliance** — Live compliance status
- **Trend Analysis** — Track improvements over time
- **Risk Heatmap** — Visual risk representation
- **Audit Trail** — Immutable compliance log
- **Certification Tracker** — Countdown to certification#

### 4. Automated Evidence Collection
- **Screenshot Capture** — Automated evidence screenshots
- **Configuration Export** — Export settings as evidence
- **Log Aggregation** — Centralized log collection
- **Policy Documentation** — Auto-generated policy docs
- **Training Records** — Employee training tracking#

### 5. Audit Readiness (Premium)
- **Pre-Audit Checklist** — 100+ control checks
- **Document Generator** — Policies, procedures, evidence
- **Auditor Portal** — Read-only access for auditors
- **Finding Tracker** — Manage audit findings
- **Remediation Workflow** — Fix non-compliant controls#

## API Endpoints#

### List Compliance Standards
```http
GET /api/v1/compliance/standards?applicable_to=healthcare
```

**Response:**
```json
{
  "standards": [
    {
      "id": "hipaa",
      "name": "Health Insurance Portability and Accountability Act",
      "region": "US",
      "industry": ["healthcare"],
      "controls_count": 164,
      "penalty_max": "$1.5M per violation",
      "ai_readiness_check": true
    }
  ]
}
```

### Run Compliance Audit
```http
POST /api/v1/compliance/audit
```

**Request:**
```json
{
  "standard": "gdpr",
  "scope": {
    "organization_id": "org_12345",
    "assets": ["web-apps", "databases", "apis"],
    "data_types": ["personal_data", "health_data"],
    "regions": ["eu", "uk"]
  },
  "audit_level": "comprehensive",  // basic, standard, comprehensive, premium
  "evidence_collection": "automatic",
  "ai_analysis": true,
  "report_format": ["pdf", "excel", "json"]
}
```

**Response:**
```json
{
  "audit_id": "audit_12345",
  "standard": "gdpr",
  "status": "in_progress",
  "progress": 15,
  "estimated_completion": "2026-05-05T23:00:00Z",
  "controls_checked": 25,
  "controls_total": 164
}
```

### Get Audit Results
```http
GET /api/v1/compliance/audit/{audit_id}
```

**Response:**
```json
{
  "audit_id": "audit_12345",
  "standard": "gdpr",
  "status": "completed",
  "overall_score": 78.5,
  "compliance_status": "partially_compliant",
  "summary": {
    "compliant": 120,
    "partially_compliant": 30,
    "non_compliant": 14
  },
  "findings": [
    {
      "control_id": "GDPR-32",
      "title": "Security of processing",
      "status": "non_compliant",
      "severity": "critical",
      "description": "Insufficient technical measures to ensure security",
      "evidence": [
        {
          "type": "screenshot",
          "url": "https://cdn.cosmicsec.com/evidence/ev_001.png"
        }
      ],
      "gap": "No encryption at rest for customer data",
      "remediation": {
        "priority": "critical",
        "steps": [
          "Enable AES-256 encryption for database",
          "Implement key management with HSM",
          "Update security policy section 4.2"
        ],
        "estimated_effort": "2 weeks",
        "cost_estimate": "$15,000"
      },
      "ai_recommendation": "Implement transparent data encryption using AWS KMS or Azure Key Vault"
    }
  ],
  "roadmap": {
    "critical": [
      {"control": "GDPR-32", "deadline": "2026-06-01"}
    ],
    "high": [
      {"control": "GDPR-25", "deadline": "2026-07-01"}
    ]
  },
  "certification_readiness": {
    "status": "not_ready",
    "blocking_issues": 14,
    "estimated_certification_date": "2026-08-15"
  },
  "ai_executive_summary": "Your organization is 78.5% compliant with GDPR. Critical gaps exist in data encryption and breach notification procedures..."
}
```

### Get Compliance Status
```http
GET /api/v1/compliance/status?organization_id=org_12345&standard=gdpr
```

**Response:**
```json
{
  "organization_id": "org_12345",
  "standard": "gdpr",
  "overall_score": 78.5,
  "status": "partially_compliant",
  "last_audit": "2026-05-01T10:00:00Z",
  "next_audit_due": "2026-08-01T10:00:00Z",
  "trend": "improving",  // improving, declining, stable
  "by_control_family": {
    "data_protection": 85.2,
    "security_measures": 72.1,
    "breach_notification": 65.3,
    "data_subject_rights": 88.7
  }
}
```

### Generate Compliance Report
```http
POST /api/v1/compliance/report
```

**Request:**
```json
{
  "audit_id": "audit_12345",
  "report_type": "executive",  // executive, technical, auditor
  "format": "pdf",
  "include_roadmap": true,
  "include_evidence": true,
  "branding": {
    "logo_url": "https://company.com/logo.png"
  }
}
```

### List Controls
```http
GET /api/v1/compliance/controls?standard=gdpr&status=non_compliant
```

**Response:**
```json
{
  "controls": [
    {
      "control_id": "GDPR-32",
      "title": "Security of processing",
      "family": "security_measures",
      "status": "non_compliant",
      "severity": "critical",
      "last_checked": "2026-05-05T22:00:00Z"
    }
  ],
  "total": 14
}
```

## CLI Usage#

```bash
# List standards
cosmicsec compliance standards --industry healthcare;

# Run audit
cosmicsec compliance audit \
  --standard gdpr \
  --org org_12345 \
  --level comprehensive \
  --ai-analysis;

# Check status
cosmicsec compliance status \
  --org org_12345 \
  --standard gdpr;

# Get results
cosmicsec compliance results audit_12345 --format json;

# Generate report
cosmicsec compliance report \
  --audit audit_12345 \
  --type executive \
  --format pdf;

# List controls
cosmicsec compliance controls \
  --standard gdpr \
  --status non_compliant
```

## Python SDK Usage#

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Run audit
audit = client.compliance.run_audit(
    standard="gdpr",
    scope={
        "organization_id": "org_12345",
        "assets": ["web-apps", "databases"]
    },
    audit_level="comprehensive",
    ai_analysis=True
)
print(f"Audit started: {audit.audit_id}")
print(f"Status: {audit.status}")

# Wait for completion
import time
while audit.status != "completed":
    audit = client.compliance.get_audit(audit.audit_id)
    print(f"Progress: {audit.progress}%")
    time.sleep(10)

# Get results
results = client.compliance.get_results(audit.audit_id)
print(f"\nOverall Score: {results.overall_score}%")
print(f"Status: {results.compliance_status}")

# Print findings
print(f"\n=== Findings ({len(results.findings)}) ===")
for finding in results.findings:
    status = "✅" if finding.status == 'compliant' else "❌"
    print(f"{status} {finding.control_id}: {finding.title}")
    print(f"   Gap: {finding.gap}")
    print(f"   AI Recommendation: {finding.ai_recommendation}")

# Generate report
report = client.compliance.generate_report(
    audit_id=audit.audit_id,
    report_type="executive",
    format="pdf"
)
print(f"\nReport generated: {report.download_url}")
```

## Configuration#

### Environment Variables
```bash
# Service
COMPLIANCE_SERVICE_PORT=8012
COMPLIANCE_SERVICE_HOST=0.0.0.0

# AI Analysis
AI_SERVICE_URL=http://ai-service:8003
AI_GAP_ANALYSIS_ENABLED=true

# Evidence Collection
EVIDENCE_DIR=/app/evidence
MAX_EVIDENCE_SIZE=104857600  # 100MB
SCREENSHOT_ENABLED=true

# OPA (Policy Engine)
OPA_URL=http://opa:8181
OPA_POLICIES_DIR=/app/policies

# Standards
ENABLED_STANDARDS=gdpr,hipaa,pci-dss,soc2,iso27001
CUSTOM_STANDARDS_DIR=/app/custom-standards

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Storage (for evidence)
MINIO_URL=http://minio:9000
EVIDENCE_BUCKET=compliance-evidence
```

## Next Steps#

- [Org Service](./org-service.md)
- [Admin Service](./admin-service.md)
- [Notification Service](./notification-service.md)
- [Compliance Guide](../guides/compliance.md)
- [GDPR Checklist](../guides/gdpr-checklist.md)
