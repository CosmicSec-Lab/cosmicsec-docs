# 🔍 Scan Service

## Overview

The **Scan Service** is the core vulnerability scanning engine. It orchestrates distributed scans across multiple targets using industry-standard tools.

**Location:** `cosmicsec-services/services/scan_service/`  
**Port:** 8002  
**Framework:** FastAPI

## Features

### 1. Distributed Scanning
- Horizontal scaling across multiple worker nodes
- Queue-based task distribution (Redis/Kafka)
- Parallel scan execution
- Resource-aware scheduling

### 2. Supported Tools
- **Nmap** — Port scanning, service detection
- **Nuclei** — Vulnerability templates
- **Nikto** — Web server scanning
- **OWASP ZAP** — Web app security
- **Gobuster** — Directory brute-force
- **Masscan** — High-speed port scan
- **SSLyze** — SSL/TLS analysis
- **Snyk** — Dependency scanning

### 3. Scan Types
- **Network Scan** — Port scanning, service enumeration
- **Web App Scan** — OWASP Top 10, injection flaws
- **API Scan** — REST/GraphQL endpoint testing
- **Container Scan** — Docker image vulnerabilities
- **Cloud Scan** — AWS/GCP/Azure misconfigurations
- **Compliance Scan** — CIS benchmarks, PCI-DSS

### 4. Scheduling & Recurrence
- One-time scans
- Recurring scans (cron expressions)
- On-demand scans
- Event-triggered scans

## API Endpoints

### Create Scan
```http
POST /api/v1/scans
```

**Request:**
```json
{
  "target": "example.com",
  "scan_type": "web_app",
  "tools": ["nmap", "nuclei", "nikto"],
  "options": {
    "ports": "1-65535",
    "rate_limit": 100,
    "timeout": 300
  },
  "schedule": {
    "type": "recurring",
    "cron": "0 2 * * *"
  }
}
```

**Response:**
```json
{
  "scan_id": "scan_12345",
  "status": "queued",
  "target": "example.com",
  "created_at": "2026-05-05T22:00:00Z",
  "estimated_duration": 300
}
```

### Get Scan Status
```http
GET /api/v1/scans/{scan_id}
```

**Response:**
```json
{
  "scan_id": "scan_12345",
  "status": "in_progress",
  "progress": 65,
  "target": "example.com",
  "started_at": "2026-05-05T22:00:00Z",
  "vulnerabilities_found": 12,
  "tools": {
    "nmap": "completed",
    "nuclei": "in_progress",
    "nikto": "pending"
  }
}
```

### List Scans
```http
GET /api/v1/scans?status=completed&limit=10&offset=0
```

**Response:**
```json
{
  "scans": [
    {
      "scan_id": "scan_12345",
      "target": "example.com",
      "status": "completed",
      "vulnerabilities_count": 15,
      "created_at": "2026-05-05T22:00:00Z"
    }
  ],
  "total": 100,
  "limit": 10,
  "offset": 0
}
```

### Cancel Scan
```http
DELETE /api/v1/scans/{scan_id}
```

**Response:**
```json
{
  "scan_id": "scan_12345",
  "status": "cancelled",
  "message": "Scan cancelled successfully"
}
```

### Get Scan Results
```http
GET /api/v1/scans/{scan_id}/results
```

**Response:**
```json
{
  "scan_id": "scan_12345",
  "target": "example.com",
  "vulnerabilities": [
    {
      "id": "vuln_001",
      "severity": "critical",
      "title": "SQL Injection in login form",
      "description": "The login form is vulnerable to SQL injection...",
      "cve": "CVE-2024-12345",
      "cvss_score": 9.8,
      "remediation": "Use parameterized queries...",
      "references": ["https://cve.mitre.org/..."]
    }
  ],
  "summary": {
    "critical": 2,
    "high": 5,
    "medium": 8,
    "low": 3,
    "info": 2
  }
}
```

### Get Scan Logs
```http
GET /api/v1/scans/{scan_id}/logs
```

**Response:**
```json
{
  "scan_id": "scan_12345",
  "logs": [
    {
      "timestamp": "2026-05-05T22:00:01Z",
      "level": "info",
      "message": "Starting Nmap scan...",
      "tool": "nmap"
    }
  ]
}
```

## Scan Workflow

```
1. User creates scan → POST /api/v1/scans
2. Scan queued in Redis/Kafka
3. Worker picks up scan task
4. Tools executed in parallel (configurable)
5. Results parsed and normalized
6. Vulnerabilities stored in PostgreSQL
7. Notifications sent (if configured)
8. Report generated (if configured)
9. Scan marked as completed
```

## Tool Configuration

### Nmap
```json
{
  "tool": "nmap",
  "options": {
    "ports": "1-65535",
    "timing": "T4",
    "scripts": ["default", "vuln"],
    "os_detection": true,
    "service_detection": true
  }
}
```

### Nuclei
```json
{
  "tool": "nuclei",
  "options": {
    "templates": ["cves", "vulnerabilities", "misconfigurations"],
    "severity": ["critical", "high", "medium"],
    "rate_limit": 100,
    "retries": 2
  }
}
```

### OWASP ZAP
```json
{
  "tool": "zap",
  "options": {
    "context": "default",
    "spider": true,
    "ajax_spider": true,
    "active_scan": true,
    "policy": "API-Minimal"
  }
}
```

## Data Models

### Scan
```python
class Scan:
    scan_id: str
    target: str
    scan_type: str  # network, web_app, api, container, cloud
    status: str  # queued, in_progress, completed, failed, cancelled
    tools: List[str]
    options: Dict
    schedule: Optional[Dict]
    created_by: str
    created_at: datetime
    started_at: Optional[datetime]
    completed_at: Optional[datetime]
    progress: int  # 0-100
    vulnerabilities_count: int
```

### Vulnerability
```python
class Vulnerability:
    vuln_id: str
    scan_id: str
    severity: str  # critical, high, medium, low, info
    title: str
    description: str
    cve: Optional[str]
    cvss_score: Optional[float]
    tool: str
    location: str  # URL, IP, port, etc.
    remediation: str
    references: List[str]
    false_positive: bool
    verified: bool
```

## Distributed Architecture

### Master-Worker Pattern
```
┌─────────────────┐
│   Scan Service  │ (Master)
│   (Port 8002)   │
└────────┬────────┘
         │
    ┌────┴────┐
    │  Redis  │ (Queue)
    └────┬────┘
         │
    ┌────┴────┐
    │         │
┌───▼───┐ ┌──▼───┐
│Worker1│ │Worker2│ (Scan Workers)
└───────┘ └──────┘
```

### Worker Configuration
```yaml
# worker-config.yml
worker:
  id: worker-1
  max_concurrent_scans: 5
  tools:
    - nmap
    - nuclei
    - nikto
  resources:
    cpu_limit: 2.0
    memory_limit: 4GB
  queue:
    type: redis
    url: redis://redis:6379
```

## Scheduling

### One-Time Scan
```json
{
  "schedule": {
    "type": "once",
    "run_at": "2026-05-06T02:00:00Z"
  }
}
```

### Recurring Scan (Cron)
```json
{
  "schedule": {
    "type": "recurring",
    "cron": "0 2 * * *",  // Every day at 2 AM
    "timezone": "UTC"
  }
}
```

### Event-Triggered Scan
```json
{
  "schedule": {
    "type": "event",
    "event": "new_asset_discovered"
  }
}
```

## Notifications

Scans can trigger notifications on:
- Scan started
- Scan completed
- Critical vulnerability found
- Scan failed

Configure via:
```json
{
  "notifications": {
    "channels": ["email", "slack", "webhook"],
    "on_start": true,
    "on_complete": true,
    "on_critical": true,
    "on_failure": true
  }
}
```

## Report Generation

### Automatic Reports
Scans can auto-generate reports:
```json
{
  "report": {
    "generate": true,
    "format": "pdf",  // pdf, html, json, csv
    "template": "executive",
    "send_to": ["security@company.com"]
  }
}
```

### Manual Report Generation
```http
POST /api/v1/scans/{scan_id}/report
```

## CLI Usage

```bash
# Create a scan
cosmicsec scan create --target example.com --type web_app

# List scans
cosmicsec scan list

# Get scan status
cosmicsec scan status scan_12345

# Cancel scan
cosmicsec scan cancel scan_12345

# Get results
cosmicsec scan results scan_12345
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="your_api_key")

# Create scan
scan = client.scans.create(
    target="example.com",
    scan_type="web_app",
    tools=["nmap", "nuclei"]
)

# Wait for completion
scan.wait()

# Get results
results = scan.get_results()
print(f"Found {len(results.vulnerabilities)} vulnerabilities")
```

## Monitoring

### Metrics
- `scan_total` — Total scans by type
- `scan_duration_seconds` — Scan duration
- `vulnerabilities_found_total` — Vulns by severity
- `scan_queue_size` — Pending scans
- `worker_active_scans` — Currently running scans

### Alerts
- Scan stuck (no progress for 30+ minutes)
- Worker down
- Queue backup (100+ pending scans)
- Critical vulnerability found

## Configuration

### Environment Variables
```bash
# Service
SCAN_SERVICE_PORT=8002
SCAN_SERVICE_HOST=0.0.0.0

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Queue
REDIS_URL=redis://redis:6379
KAFKA_BROKERS=kafka:9092

# Tools
NMAP_PATH=/usr/bin/nmap
NUCLEI_PATH=/usr/local/bin/nuclei
NIKTO_PATH=/usr/bin/nikto

# Workers
MAX_CONCURRENT_SCANS=10
WORKER_TIMEOUT=3600

# Notifications
NOTIFICATION_SERVICE_URL=http://notification-service:8011
```

## Security Considerations

1. **Target Validation** — Prevent scanning internal/forbidden targets
2. **Rate Limiting** — Prevent abuse of scanning resources
3. **Tool Isolation** — Run tools in sandboxed containers
4. **Credential Management** — Secure storage of auth credentials
5. **Result Sanitization** — Prevent XSS in vulnerability descriptions
6. **Audit Logging** — Log all scan activities

## Troubleshooting

### Scan Stuck
```bash
# Check scan status
curl http://localhost:8002/api/v1/scans/scan_12345

# Check worker logs
docker logs scan-worker-1

# Restart worker
docker restart scan-worker-1
```

### Tool Not Found
```bash
# Verify tool installation
docker exec -it scan-service nmap --version
docker exec -it scan-service nuclei --version

# Check PATH
docker exec -it scan-service echo $PATH
```

### Queue Backup
```bash
# Check queue size
redis-cli -h redis LLEN scan_queue

# Add more workers
docker-compose up -d --scale scan-worker=5
```

## Next Steps

- [Recon Service](./recon-service.md)
- [Report Service](./report-service.md)
- [AI Service](./ai-service.md)
- [CLI Scan Commands](../cli/commands.md)
- [Python SDK Scans](../sdks/python-sdk.md)
