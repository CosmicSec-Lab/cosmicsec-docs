# 🔐 Admin Service#

## Overview#

The **Admin Service** provides super-administrator capabilities for platform-wide management, system monitoring, and premium enterprise support features.

**Location:** `cosmicsec-services/services/admin_service/`  
**Port:** 8014  
**Framework:** FastAPI#

## Premium Features#

### 1. System-Wide Dashboard
- **Real-Time Health** — All 25+ services status
- **Resource Usage** — CPU, memory, disk across cluster
- **Request Volume** — Total RPS, error rates
- **Revenue Metrics** — MRR, churn, upgrades
- **Geographic Distribution** — Users by country/region#

### 2. User Management (Global)
- **Cross-Org Search** — Find users across all orgs
- **Impersonation** — Support staff can login as user
- **Bulk Actions** — Activate/deactivate many users
- **Audit Log** — Every admin action logged
- **Permission Review** — See who has access to what#

### 3. System Configuration
- **Feature Flags** — Enable/disable features globally
- **Rate Limits** — Platform-wide throttling
- **Maintenance Mode** — Take services offline gracefully
- **API Versioning** — Manage multiple API versions
- **Security Policies** — Password complexity, 2FA enforcement#

### 4. Backup & Disaster Recovery
- **Automated Backups** — Database, files, configs
- **Point-in-Time Restore** — Restore to any backup
- **Cross-Region Replication** — DR to another region
- **Backup Verification** — Automated integrity checks
- **RTO/RPO Monitoring** — Recovery time/point objectives#

### 5. Enterprise Support
- **Priority Queue** — Elite customers get priority
- **SLA Monitoring** — Track platform SLAs
- **Escalation Management** — Critical issues workflow
- **Customer Health Score** — Predictive churn
- **Usage Analytics** — Deep insights per customer#

## API Endpoints#

### Get System Dashboard
```http
GET /api/v1/admin/dashboard?timeframe=24h
```

**Response:**
```json
{
  "timestamp": "2026-05-05T22:00:00Z",
  "services": {
    "total": 25,
    "healthy": 24,
    "degraded": 1,
    "down": 0
  },
  "resources": {
    "cpu_usage_avg": 42.3,
    "memory_usage_avg": 58.7,
    "disk_usage_avg": 34.5
  },
  "requests": {
    "total_24h": 1500000,
    "rps_current": 250,
    "error_rate": 0.02,
    "p95_latency_ms": 850
  },
  "business": {
    "mrr_usd": 450000,
    "customers_total": 450,
    "churn_rate": 0.02,
    "nps_score": 72
  }
}
```

### Get System Health
```http
GET /api/v1/admin/system/health?service=all
```

**Response:**
```json
{
  "services": [
    {
      "service_id": "api-gateway",
      "status": "healthy",
      "uptime_seconds": 259200,
      "cpu_usage": 35.2,
      "memory_usage": 512,
      "active_connections": 150
    },
    {
      "service_id": "scan-service",
      "status": "degraded",
      "uptime_seconds": 259100,
      "cpu_usage": 85.3,
      "memory_usage": 2048,
      "error_rate": 0.15
    }
  ]
}
```

### Get System Metrics
```http
GET /api/v1/admin/system/metrics?metric=cpu_usage&timeframe=24h
```

**Response:**
```json
{
  "metric": "cpu_usage",
  "timeframe": "24h",
  "data_points": [
    {"timestamp": "2026-05-05T00:00:00Z", "value": 38.5},
    {"timestamp": "2026-05-05T01:00:00Z", "value": 42.1}
  ],
  "summary": {
    "min": 28.3,
    "max": 85.3,
    "avg": 42.3,
    "p95": 78.5
  }
}
```

### Toggle Maintenance Mode
```http
POST /api/v1/admin/maintenance/mode
```

**Request:**
```json
{
  "enabled": true,
  "services": ["api-gateway", "scan-service"],
  "message": "Scheduled maintenance. Back in 30 minutes.",
  "duration_minutes": 30,
  "allowed_ips": ["192.168.1.0/24"]
}
```

### Get System Logs
```http
GET /api/v1/admin/logs?service=scan-service&level=error&limit=100
```

**Response:**
```json
{
  "logs": [
    {
      "timestamp": "2026-05-05T22:00:00Z",
      "service": "scan-service",
      "level": "error",
      "message": "Failed to connect to Redis",
      "traceback": "..."
    }
  ],
  "total": 150
}
```

### Update System Settings
```http
PUT /api/v1/admin/settings
```

**Request:**
```json
{
  "feature_flags": {
    "new_ui_enabled": true,
    "experimental_ai_enabled": false
  },
  "rate_limits": {
    "global_requests_per_minute": 10000,
    "per_user_requests_per_minute": 100
  },
  "security": {
    "require_2fa_for_all": true,
    "min_password_length": 12,
    "session_timeout_seconds": 3600
  }
}
```

### Trigger Backup
```http
POST /api/v1/admin/backup
```

**Request:**
```json
{
  "type": "full",  // full, incremental, config-only
  "services": ["all"],
  "storage_location": "s3://cosmicsec-backups/2026-05-05/",
  "verify": true
}
```

**Response:**
```json
{
  "backup_id": "backup_12345",
  "status": "in_progress",
  "estimated_size_gb": 150.5,
  "estimated_duration_minutes": 45
}
```

### Get Backup Status
```http
GET /api/v1/admin/backup/status?backup_id=backup_12345
```

## CLI Usage#

```bash
# Get system dashboard
cosmicsec admin dashboard --timeframe 24h;

# Check system health
cosmicsec admin health --service all;

# Get system metrics
cosmicsec admin metrics \
  --metric cpu_usage \
  --timeframe 24h;

# Toggle maintenance mode
cosmicsec admin maintenance-on \
  --services api-gateway,scan-service \
  --duration 30 \
  --message "Maintenance in progress";

# View system logs
cosmicsec admin logs \
  --service scan-service \
  --level error \
  --limit 100;

# Update settings
cosmicsec admin settings update \
  --set global_rate_limit=10000 \
  --set require_2fa=true;

# Trigger backup
cosmicsec admin backup \
  --type full \
  --verify;

# Check backup status
cosmicsec admin backup status backup_12345
```

## Python SDK Usage#

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")  # Must be super_admin

# Get dashboard
dashboard = client.admin.get_dashboard(timeframe="24h")
print(f"Services: {dashboard.services['healthy']}/{dashboard.services['total']} healthy")
print(f"RPS: {dashboard.requests['rps_current']}")
print(f"MRR: ${dashboard.business['mrr_usd']}")

# Check system health
health = client.admin.get_health()
print(f"\nService Status:")
for svc in health.services:
    status = "✅" if svc.status == 'healthy' else "⚠️" if svc.status == 'degraded' else "❌"
    print(f"{status} {svc.service_id}: CPU {svc.cpu_usage}%")

# Get metrics
metrics = client.admin.get_metrics(
    metric="cpu_usage",
    timeframe="24h"
)
print(f"\nCPU Usage (24h):")
print(f"  Avg: {metrics.summary['avg']}%")
print(f"  Max: {metrics.summary['max']}%")

# Enable maintenance mode
client.admin.toggle_maintenance(
    enabled=True,
    services=["api-gateway"],
    message="Maintenance in progress",
    duration_minutes=30
)
print("Maintenance mode enabled")

# Trigger backup
backup = client.admin.trigger_backup(
    type="full",
    verify=True
)
print(f"Backup started: {backup.backup_id}")
print(f"Estimated size: {backup.estimated_size_gb}GB")
```

## Configuration#

### Environment Variables
```bash
# Service
ADMIN_SERVICE_PORT=8014
ADMIN_SERVICE_HOST=0.0.0.0

# Feature Flags
NEW_UI_ENABLED=true
EXPERIMENTAL_AI_ENABLED=false
BETA_FEATURES_ENABLED=false

# Maintenance
MAINTENANCE_MODE_DEFAULT=false
ALLOWED_IPS_IN_MAINTENANCE=127.0.0.1,::1

# Backup
BACKUP_DIR=/backups
S3_BACKUP_BUCKET=s3://cosmicsec-backups/
BACKUP_VERIFICATION_ENABLED=true
RETENTION_DAYS=90

# Audit
AUDIT_LOG_ENABLED=true
AUDIT_LOG_RETENTION_DAYS=365
ADMIN_ACTION_APPROVAL_ENABLED=true

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Monitoring#

### Prometheus Metrics
- `admin_services_healthy` — Healthy service count
- `admin_system_cpu_usage` — System CPU average
- `admin_system_memory_usage` — System memory average
- `admin_requests_total` — Total requests (24h)
- `admin_mrr_usd` — Monthly recurring revenue
- `admin_customers_total` — Total customer count#

### Alerts
- Service down > 5 minutes
- CPU usage > 90% for 15 minutes
- Error rate > 5% for 10 minutes
- Backup failed
- MRR dropped > 10%

## Troubleshooting#

### Service Down
```bash
# Check service status
curl http://localhost:8014/api/v1/admin/system/health

# View logs
curl http://localhost:8014/api/v1/admin/logs?level=error

# Restart service
docker restart scan-service
```

### High Error Rate
```bash
# Get error logs
curl http://localhost:8014/api/v1/admin/logs?level=error&limit=100

# Check rate limits
curl http://localhost:8014/api/v1/admin/settings | jq .rate_limits

# Scale service
kubectl scale deployment scan-service --replicas=5
```

### Backup Fails
```bash
# Check backup status
curl http://localhost:8014/api/v1/admin/backup/status?backup_id=backup_12345

# Verify S3 connectivity
aws s3 ls s3://cosmicsec-backups/

# Check disk space
df -h /backups
```

## Next Steps#

- [Egress Service](./egress-service.md)
- [Ingest Service](./ingest-service.md)
- [API Gateway](../architecture/gateway.md)
- [System Administration Guide](../guides/system-admin.md)
- [Disaster Recovery Plan](../guides/disaster-recovery.md)
