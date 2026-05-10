# 📈 SLA Manager Service#

## Overview#

The **SLA Manager Service** provides enterprise-grade Service Level Agreement tracking with 4 tiers, automated penalty calculations, and real-time compliance monitoring.

**Location:** `cosmicsec-services/services/sla_manager/`  
**Port:** 8028  
**Framework:** FastAPI

## Premium Features#

### 1. SLA Tiers (4 Levels)
| Tier | Uptime Guarantee | Response Time | Penalty Rate | Price Multiplier |
|------|----------------|-----------------|--------------|-------------------|
| **Basic** | 99.0% | 5s p95 | 5% credit/month | 1.0x |
| **Professional** | 99.5% | 2s p95 | 10% credit/month | 1.5x |
| **Enterprise** | 99.9% | 1s p95 | 15% credit/month | 2.0x |
| **Elite** | 99.95% | 500ms p95 | 25% credit/month | 3.0x |

### 2. Automated Metrics Collection
- **Uptime Monitoring** — HTTP(S) endpoint checks (30s interval)
- **Response Time** — p50, p95, p99 percentiles
- **Error Rate** — 4xx, 5xx tracking
- **Throughput** — Requests/second
- **Saturation** — CPU, memory, disk usage

### 3. Penalty Calculation Engine
```python
# Penalty calculation logic
def calculate_penalty(sla_tier, uptime_percentage, month_days):
    guaranteed_uptime = SLA_TIERS[sla_tier]['uptime_guarantee']
    
    if uptime_percentage >= guaranteed_uptime:
        return 0.0  # No penalty
    
    downtime_percentage = guaranteed_uptime - uptime_percentage
    penalty_rate = SLA_TIERS[sla_tier]['penalty_rate']
    
    # Pro-rated penalty
    penalty = (downtime_percentage / (100 - guaranteed_uptime)) * penalty_rate
    return min(penalty, 1.0)  # Cap at 100%
```

### 4. Real-Time Compliance Dashboard
- **Current SLA Status** — Met/violated in real-time
- **Rolling 30-Day Window** — Continuous compliance
- **Penalty Projections** — Forecast month-end penalties
- **Service Credits** — Automatic credit calculation
- **Breach Notifications** — Immediate SLA violation alerts

### 5. Custom SLA Definitions
- **Service-Specific SLAs** — Different tiers per service
- **Composite SLAs** — Combine multiple services
- **Business Hour SLAs** — 9-5 only, exclude weekends
- **Seasonal Adjustments** — Holiday/peak adjustments

## API Endpoints#

### Subscribe to SLA Tier
```http
POST /api/v1/sla/subscribe
```

**Request:**
```json
{
  "organization_id": "org_12345",
  "tier": "enterprise",
  "services": ["api-gateway", "scan-service", "ai-service"],
  "custom_uptime_guarantee": 99.9,  // Override default
  "custom_response_time_ms": 1000,  // p95 target
  "penalty_preference": "automatic_credit",  // automatic_credit, notify_only, waive
  "billing_cycle": "monthly"
}
```

**Response:**
```json
{
  "sla_id": "sla_12345",
  "tier": "enterprise",
  "status": "active",
  "services": ["api-gateway", "scan-service", "ai-service"],
  "guarantees": {
    "uptime_percentage": 99.9,
    "response_time_p95_ms": 1000,
    "error_rate_threshold": 0.1
  },
  "penalty_rate": 0.15,
  "created_at": "2026-05-05T22:00:00Z",
  "next_assessment": "2026-06-01T00:00:00Z"
}
```

### Get SLA Status
```http
GET /api/v1/sla/status?organization_id=org_12345&timeframe=30d
```

**Response:**
```json
{
  "organization_id": "org_12345",
  "sla_id": "sla_12345",
  "tier": "enterprise",
  "timeframe": "30d",
  "current_status": "compliant",  // compliant, violated, at_risk
  "metrics": {
    "uptime_percentage": 99.92,
    "response_time_p95_ms": 850,
    "error_rate": 0.05,
    "throughput_rps": 8500
  },
  "guarantees": {
    "uptime_percentage": 99.9,
    "response_time_p95_ms": 1000,
    "error_rate_threshold": 0.1
  },
  "status_per_metric": {
    "uptime": "compliant",
    "response_time": "compliant",
    "error_rate": "compliant"
  },
  "days_in_compliance": 28,
  "days_violated": 2,
  "current_penalty": 0.0
}
```

### Get SLA Metrics
```http
GET /api/v1/sla/metrics?service=api-gateway&timeframe=24h&resolution=5m
```

**Response:**
```json
{
  "service": "api-gateway",
  "timeframe": "24h",
  "resolution": "5m",
  "metrics": [
    {
      "timestamp": "2026-05-05T00:00:00Z",
      "uptime": 1.0,  // 1.0 = up, 0.0 = down
      "response_time_ms": 850,
      "error_rate": 0.02,
      "requests_per_second": 8500
    }
  ],
  "summary": {
    "avg_uptime": 0.999,
    "avg_response_time_ms": 920,
    "p50_response_time_ms": 750,
    "p95_response_time_ms": 1200,
    "p99_response_time_ms": 2500,
    "avg_error_rate": 0.03
  }
}
```

### List Penalty Events
```http
GET /api/v1/sla/penalties?organization_id=org_12345&year=2026&month=5
```

**Response:**
```json
{
  "organization_id": "org_12345",
  "period": "2026-05",
  "penalties": [
    {
      "penalty_id": "pen_001",
      "date": "2026-05-10",
      "service": "scan-service",
      "violation_type": "uptime",
      "guaranteed": 99.9,
      "actual": 99.85,
      "downtime_minutes": 36,
      "penalty_amount": 150.00,
      "currency": "USD",
      "status": "credited",  // credited, pending, disputed
      "incident_id": "incident_12345"
    }
  ],
  "total_penalties": 2,
  "total_credit": 300.00,
  "status": "below_threshold"  // compliant, at_risk, violated
}
```

### Dispute Penalty
```http
POST /api/v1/sla/penalties/dispute
```

**Request:**
```json
{
  "penalty_id": "pen_001",
  "reason": "Scheduled maintenance with customer notification",
  "evidence": "https://company.com/maintenance-notice",
  "requested_adjustment": "waive"
}
```

**Response:**
```json
{
  "dispute_id": "disp_001",
  "status": "under_review",
  "estimated_resolution": "2026-05-10T22:00:00Z"
}
```

## SLA Monitoring Architecture#

```
┌─────────────────────────────────────────────────────────────┐
│              SLA Manager (Port 8028)                    │
├─────────────────────────────────────────────────────────────┤
│  Metrics Collection (Every 30s)                        │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐            │
│  │API      │  │Scan    │  │AI       │  ...       │
│  │Gateway  │  │Service │  │Service  │  25+ svc  │
│  └────┬────┘  └────┬────┘  └────┬────┘            │
│       └──────────┬──────────┘                    │
│                  ▼                                   │
│  ┌──────────────────────────────────────┐          │
│  │  Metrics Aggregator (Prometheus)         │          │
│  └──────────────────────┬───────────────┘          │
│                         ▼                          │
│  ┌──────────────────────────────────────┐          │
│  │  SLA Evaluation Engine                    │          │
│  │  • Check uptime >= guarantee?            │          │
│  │  • Check response time <= p95 target?   │          │
│  │  • Check error rate <= threshold?       │          │
│  └──────────────────────┬───────────────┘          │
│                         ▼                          │
│  ┌──────────────────────────────────────┐          │
│  │  Penalty Calculator                     │          │
│  │  • Calculate downtime                   │          │
│  │  • Apply penalty rate                  │          │
│  │  • Generate credit note                 │          │
│  └──────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

## Penalty Calculation Examples#

### Example 1: Basic Tier Violation
```yaml
Tier: Basic
Guaranteed Uptime: 99.0%
Actual Uptime: 98.5%
Downtime: 0.5%

Calculation:
  penalty = (0.5 / (100 - 99.0)) * 5%
  penalty = (0.5 / 1.0) * 0.05
  penalty = 0.025 (2.5% credit)

For $1000/month bill:
  Credit = $1000 * 0.025 = $25.00
```

### Example 2: Enterprise Tier Violation
```yaml
Tier: Enterprise
Guaranteed Uptime: 99.9%
Actual Uptime: 99.8%
Downtime: 0.1%

Calculation:
  penalty = (0.1 / (100 - 99.9)) * 15%
  penalty = (0.1 / 0.1) * 0.15
  penalty = 0.15 (15% credit)

For $5000/month bill:
  Credit = $5000 * 0.15 = $750.00
```

## Custom SLA Definition#

### Business Hours Only (9-5, M-F)
```json
{
  "name": "Business Hours SLA",
  "tier": "custom",
  "schedule": {
    "days": ["mon", "tue", "wed", "thu", "fri"],
    "hours": {"start": "09:00", "end": "17:00"},
    "timezone": "America/New_York",
    "exclude_holidays": true
  },
  "guarantees": {
    "uptime_percentage": 99.5,
    "response_time_p95_ms": 2000
  },
  "penalty_rate": 0.10
}
```

### Composite SLA (Multi-Service)
```json
{
  "name": "Platform-Wide SLA",
  "type": "composite",
  "services": [
    {"name": "api-gateway", "weight": 0.3},
    {"name": "scan-service", "weight": 0.4},
    {"name": "ai-service", "weight": 0.3}
  ],
  "calculation": "weighted_average",
  "guaranteed_uptime": 99.9
}
```

## CLI Usage#

```bash
# Subscribe to SLA tier
cosmicsec sla subscribe \
  --tier enterprise \
  --services api-gateway,scan-service,ai-service \
  --org org_12345;

# Check SLA status
cosmicsec sla status \
  --org org_12345 \
  --timeframe 30d;

# View metrics
cosmicsec sla metrics \
  --service api-gateway \
  --timeframe 24h \
  --resolution 5m;

# List penalties
cosmicsec sla penalties \
  --org org_12345 \
  --year 2026 --month 5;

# Dispute penalty
cosmicsec sla dispute \
  --penalty pen_001 \
  --reason "Scheduled maintenance" \
  --evidence "https://...";

# Forecast month-end
cosmicsec sla forecast \
  --org org_12345 \
  --month 2026-05
```

## Python SDK Usage#

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Subscribe to SLA
sla = client.sla.subscribe(
    organization_id="org_12345",
    tier="enterprise",
    services=["api-gateway", "scan-service", "ai-service"]
)
print(f"SLA subscribed: {sla.sla_id}")
print(f"Tier: {sla.tier}")
print(f"Penalty rate: {sla.penalty_rate*100}%")

# Check status
status = client.sla.get_status(
    organization_id="org_12345",
    timeframe="30d"
)
print(f"\nCurrent status: {status.current_status}")
print(f"Uptime: {status.metrics['uptime_percentage']}%")
print(f"Response time p95: {status.metrics['response_time_p95_ms']}ms")

# Get metrics
metrics = client.sla.get_metrics(
    service="api-gateway",
    timeframe="24h",
    resolution="5m"
)
print(f"\nAverage uptime: {metrics.summary['avg_uptime']*100:.2f}%")
print(f"p95 response time: {metrics.summary['p95_response_time_ms']}ms")

# List penalties
penalties = client.sla.list_penalties(
    organization_id="org_12345",
    year=2026,
    month=5
)
print(f"\nTotal penalties: {penalties.total_penalties}")
print(f"Total credit: ${penalties.total_credit}")

for pen in penalties.penalties:
    print(f"  {pen.date}: {pen.service} - ${pen.penalty_amount} ({pen.status})")

# Forecast month-end
forecast = client.sla.forecast(
    organization_id="org_12345",
    month="2026-05"
)
print(f"\nForecast: {forecast.projected_status}")
print(f"Projected penalty: ${forecast.projected_penalty}")
```

## Grafana Dashboard#

Pre-built dashboard shows:
- **SLA Compliance Gauge** — Current compliance %
- **Uptime Trend** — 30-day rolling window
- **Response Time Heatmap** — p50/p95/p99 over time
- **Penalty Events** — Timeline of violations
- **Service Credits** — Monthly credit totals
- **Downtime Analysis** — By service, day, hour

## Configuration#

### Environment Variables
```bash
# Service
SLA_SERVICE_PORT=8028
SLA_SERVICE_HOST=0.0.0.0

# Metrics Collection
METRICS_INTERVAL=30
PROMETHEUS_URL=http://prometheus:9090
RETENTION_DAYS=90

# Penalty Settings
DEFAULT_PENALTY_CALCULATION=pro_rated
MAX_PENALTY_PERCENTAGE=1.0  # Cap at 100%
AUTO_CREDIT_ENABLED=true

# Notifications
SLACK_WEBHOOK_URL=https://hooks.slack.com/...
NOTIFICATION_EMAIL=sla@company.com
ALERT_THRESHOLD=0.05  # Alert if penalty > 5%

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Troubleshooting#

### SLA Showing as Violated Incorrectly
```bash
# Check raw metrics
curl http://localhost:9090/api/v1/query?query=up{job="api-gateway"}

# Verify calculation
curl http://localhost:8028/api/v1/sla/debug/sla_12345

# Check exclusions (maintenance windows)
curl http://localhost:8028/api/v1/sla/sla_12345/exclusions
```

### Penalty Not Applied
```bash
# Check penalty calculation
curl http://localhost:8028/api/v1/sla/penalties/pen_001

# Verify auto-credit enabled
docker exec sla-manager env | grep AUTO_CREDIT

# Check billing integration
docker logs sla-manager | grep "billing"
```

### Metrics Not Updating
```bash
# Check Prometheus target
curl http://localhost:9090/api/v1/targets

# Verify metrics endpoint
curl http://localhost:8028/metrics | grep sla

# Check service discovery
curl http://localhost:8000/api/v1/gateway/services | grep sla-manager
```

## Next Steps#

- [Theme Builder](./theme-builder.md)
- [Onboarding Wizard](./onboarding-wizard.md)
- [NLP Search](./nlp-search.md)
- [Notification Service](./notification-service.md)
- [SLA Management Guide](../guides/sla-management.md)
