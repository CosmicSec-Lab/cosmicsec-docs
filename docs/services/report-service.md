# 🔍 Report Service

## Overview

The **Report Service** generates multi-format security reports with AI-powered executive summaries. It supports PDF, HTML, JSON, CSV, and Excel formats with custom branding.

**Location:** `cosmicsec-services/services/report_service/`  
**Port:** 8005  
**Framework:** FastAPI

## Features

### 1. Multi-Format Export
- **PDF** — Professional formatted reports (executive, technical)
- **HTML** — Interactive web reports with charts
- **JSON** — Machine-readable structured data
- **CSV** — Spreadsheet-compatible format
- **Excel** — Multi-sheet workbooks with formatting
- **STIX** — Industry-standard threat intel format

### 2. Template Engine
- **Executive Template** — C-level summary, business risk
- **Technical Template** — Detailed findings, remediation steps
- **Compliance Template** — Regulatory compliance focus
- **Custom Templates** — User-defined templates with Jinja2

### 3. AI-Powered Summaries
- Automatic executive summary generation
- Key findings extraction
- Risk scoring and trend analysis
- Natural language recommendations

### 4. Scheduled Reports
- Daily, weekly, monthly reports
- Event-triggered reports (scan complete, critical vuln found)
- Email delivery with attachments
- Slack/Teams webhook integration

### 5. Custom Branding
- Company logo insertion
- Custom color schemes
- Disclaimer/footer customization
- Watermark options

## API Endpoints

### Generate Report
```http
POST /api/v1/reports/generate
```

**Request:**
```json
{
  "scan_id": "scan_12345",
  "report_type": "executive",
  "format": "pdf",
  "options": {
    "branding": {
      "logo_url": "https://company.com/logo.png",
      "primary_color": "#6366f1",
      "footer_text": "Confidential - Acme Corp"
    },
    "sections": ["summary", "vulnerabilities", "remediation"],
    "ai_summary": true
  }
}
```

**Response:**
```json
{
  "report_id": "report_12345",
  "status": "generating",
  "estimated_time": 30,
  "download_url": null
}
```

### Get Report Status
```http
GET /api/v1/reports/{report_id}
```

**Response:**
```json
{
  "report_id": "report_12345",
  "scan_id": "scan_12345",
  "report_type": "executive",
  "format": "pdf",
  "status": "completed",
  "created_at": "2026-05-05T22:00:00Z",
  "completed_at": "2026-05-05T22:00:30Z",
  "download_url": "http://localhost:8005/api/v1/reports/report_12345/download",
  "file_size": 2048576,
  "page_count": 15
}
```

### List Reports
```http
GET /api/v1/reports?report_type=executive&limit=10&offset=0
```

### Download Report
```http
GET /api/v1/reports/{report_id}/download
```
Returns: Binary file (PDF/HTML/JSON/CSV/Excel)

### Delete Report
```http
DELETE /api/v1/reports/{report_id}
```

### Share Report
```http
POST /api/v1/reports/{report_id}/share
```

**Request:**
```json
{
  "emails": ["manager@company.com", "cto@company.com"],
  "message": "Q1 Security Report attached.",
  "expires_at": "2026-06-05T22:00:00Z"
}
```

## Report Types

### Executive Report
**Audience:** C-Level, Management

**Sections:**
1. Executive Summary (AI-generated)
2. Risk Score & Trends
3. Critical Findings (top 10)
4. Business Impact Analysis
5. Compliance Status
6. Recommendations
7. Appendix: Full Findings

**Sample Output:**
```
📊 EXECUTIVE SECURITY REPORT - Q1 2026

Executive Summary:
The security assessment of example.com revealed 15 critical 
vulnerabilities requiring immediate attention. The overall risk 
score is 8.5/10 (High). Immediate action is recommended...

Key Findings:
• 15 Critical SQL Injection vulnerabilities
• 8 High-severity XSS issues
• 12 Medium-severity configuration flaws

Business Impact:
• Potential data breach cost: $2.3M (estimated)
• Regulatory fines: Up to $500K
• Reputation damage: High

Recommendations:
1. Implement parameterized queries (Critical)
2. Deploy WAF rules (High)
3. ...
```

### Technical Report
**Audience:** Security Team, IT, Developers

**Sections:**
1. Methodology
2. Detailed Findings (all vulnerabilities)
3. Proof of Concept (PoC) code
4. Remediation Steps
5. Configuration Fixes
6. Verification Steps
7. References (CVE, OWASP)

### Compliance Report
**Audience:** Auditors, Compliance Team

**Sections:**
1. Compliance Status (GDPR, HIPAA, PCI-DSS)
2. Control Mapping
3. Findings by Regulation
4. Remediation Timeline
5. Audit Trail
6. Certification Readiness

## Template Customization

### Create Custom Template
```http
POST /api/v1/reports/templates
```

**Request:**
```json
{
  "name": "My Company Template",
  "base_template": "executive",
  "branding": {
    "logo_url": "https://mycompany.com/logo.png",
    "primary_color": "#ff0000",
    "font_family": "Arial"
  },
  "sections": [
    {"type": "custom_header", "content": "Confidential"},
    {"type": "executive_summary", "ai_generated": true},
    {"type": "vulnerabilities", "group_by": "severity"},
    {"type": "custom_footer", "content": "© 2026 My Company"}
  ]
}
```

### Jinja2 Template Example
```jinja2
<!-- templates/custom_report.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{{ report.title }}</title>
    <style>
        body { font-family: {{ branding.font_family }}; }
        .header { background-color: {{ branding.primary_color }}; }
    </style>
</head>
<body>
    <div class="header">
        <img src="{{ branding.logo_url }}" />
        <h1>{{ report.title }}</h1>
    </div>
    
    <div class="executive-summary">
        {{ ai_summary | safe }}
    </div>
    
    <div class="findings">
        {% for vuln in vulnerabilities %}
        <div class="vulnerability {{ vuln.severity }}">
            <h3>{{ vuln.title }}</h3>
            <p>{{ vuln.description }}</p>
        </div>
        {% endfor %}
    </div>
</body>
</html>
```

## Scheduled Reports

### Create Schedule
```http
POST /api/v1/reports/schedule
```

**Request:**
```json
{
  "name": "Weekly Executive Report",
  "schedule": {
    "type": "recurring",
    "cron": "0 8 * * MON",
    "timezone": "America/New_York"
  },
  "report_config": {
    "report_type": "executive",
    "format": "pdf",
    "scan_filter": {
      "created_after": "last_7_days"
    }
  },
  "delivery": {
    "method": "email",
    "recipients": ["manager@company.com"],
    "subject": "Weekly Security Report"
  },
  "enabled": true
}
```

## AI Summary Generation

### Example AI-Generated Summary
```python
{
  "executive_summary": """
    Based on the comprehensive security scan conducted on example.com, 
    the system has identified a total of 35 vulnerabilities across 
    web applications and network infrastructure. 
    
    **Critical Findings:**
    - 15 SQL injection vulnerabilities in the login module
    - 8 cross-site scripting (XSS) flaws in user input forms
    - 5 remote code execution (RCE) vulnerabilities
    
    **Risk Assessment:**
    The overall security posture is rated as HIGH RISK. Immediate 
    remediation is required for critical vulnerabilities to prevent 
    potential data breaches.
    
    **Business Impact:**
    If left unaddressed, these vulnerabilities could result in:
    - Unauthorized access to customer data (est. 500K records)
    - Regulatory fines up to $2M (GDPR violations)
    - Reputational damage and loss of customer trust
    
    **Immediate Actions Required:**
    1. Implement input validation and parameterized queries
    2. Deploy Web Application Firewall (WAF)
    3. Conduct emergency patch deployment
  """,
  "key_metrics": {
    "total_vulnerabilities": 35,
    "critical_count": 15,
    "risk_score": 8.5,
    "estimated_remediation_time": "40 hours"
  }
}
```

## CLI Usage

```bash
# Generate report
cosmicsec report generate \
  --scan-id scan_12345 \
  --type executive \
  --format pdf

# List reports
cosmicsec report list --limit 10

# Download report
cosmicsec report download report_12345 --output ./reports/

# Share report
cosmicsec report share report_12345 \
  --emails manager@company.com \
  --message "Q1 Security Report"
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Generate report
report = client.reports.generate(
    scan_id="scan_12345",
    report_type="executive",
    format="pdf",
    options={
        "ai_summary": True,
        "branding": {
            "logo_url": "https://company.com/logo.png"
        }
    }
)

print(f"Report generating: {report.report_id}")
print(f"Status: {report.status}")

# Wait for completion
import time
while report.status != "completed":
    report = client.reports.get(report.report_id)
    time.sleep(5)

# Download
client.reports.download(
    report_id=report.report_id,
    output_path="./reports/"
)
print("Report downloaded!")
```

## Configuration

### Environment Variables
```bash
# Service
REPORT_SERVICE_PORT=8005
REPORT_SERVICE_HOST=0.0.0.0

# Storage
MINIO_URL=http://minio:9000
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin

# AI Summary Generation
AI_SERVICE_URL=http://ai-service:8003
AI_SUMMARY_ENABLED=true

# Templates
TEMPLATES_DIR=/app/templates
CUSTOM_TEMPLATES_ENABLED=true

# Delivery
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SLACK_WEBHOOK_URL=https://hooks.slack.com/...

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Report Generation Pipeline

```
Scan Results → Report Service → Template Engine
                                    ↓
                              AI Summary (optional)
                                    ↓
                              Format Conversion
                                    ↓
                              Branding & Styling
                                    ↓
                              Store in MinIO
                                    ↓
                              Email/Slack Delivery
```

### Step-by-Step
1. **Fetch Data:** Retrieve scan results from database
2. **Apply Template:** Use Jinja2/HTML template
3. **AI Summary:** Generate executive summary (if enabled)
4. **Format Conversion:** Convert to PDF/HTML/JSON/CSV/Excel
5. **Apply Branding:** Insert logo, colors, disclaimers
6. **Store:** Upload to MinIO object storage
7. **Deliver:** Send via email/Slack/webhook

## Troubleshooting

### Report Generation Fails
```bash
# Check logs
docker logs report-service | grep "report_12345"

# Check MinIO connectivity
docker exec report-service curl http://minio:9000/minio/health/live

# Verify template exists
docker exec report-service ls /app/templates/
```

### PDF Generation Issues
```bash
# Check wkhtmltopdf installation
docker exec report-service which wkhtmltopdf

# Install if missing
docker exec report-service apt-get update && apt-get install -y wkhtmltopdf
```

### AI Summary Not Generated
```bash
# Check AI service connectivity
curl http://localhost:8003/health

# Verify AI_SUMMARY_ENABLED
docker exec report-service env | grep AI_SUMMARY
```

## Next Steps

- [Scan Service](./scan-service.md)
- [AI Service](./ai-service.md)
- [Compliance Service](../services/compliance-service.md)
- [CLI Report Commands](../cli/commands.md#report)
- [Python SDK Reports](../sdks/python-sdk.md#reports)
