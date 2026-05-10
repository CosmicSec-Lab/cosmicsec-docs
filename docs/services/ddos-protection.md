# 🛡️ DDoS Protection Service

## Overview

The **DDoS Protection Service** provides enterprise-grade DDoS mitigation capable of handling 10K+ RPS (Requests Per Second). Features challenge-response, rate limiting, geofencing, and auto-mitigation.

**Location:** `cosmicsec-services/services/ddos_protection/`  
**Port:** 8021  
**Framework:** FastAPI + Redis + Nginx

## Features

### 1. High-Volume Mitigation
- **10K+ RPS Handling** — Tested up to 100K RPS
- **Layer 3/4 Protection** — Network/transport layer attacks
- **Layer 7 Protection** — Application layer attacks
- **Volumetric Attack Detection** — Bandwidth saturation
- **Protocol Attack Detection** — SYN floods, ping floods

### 2. Challenge-Response
- **JavaScript Challenge** — Browser verification
- **CAPTCHA** — reCAPTCHA v3 integration
- **Proof of Work** — Computational challenges
- **Behavioral Analysis** — Human vs bot detection
- **Device Fingerprinting** — Track unique devices

### 3. Rate Limiting
- **Per-IP Limits** — Configurable request thresholds
- **Per-Endpoint Limits** — Different limits by API path
- **Sliding Window** — Accurate rate counting
- **Burst Allowance** — Short bursts permitted
- **Whitelist/Blacklist** — IP-based access control

### 4. Geofencing
- **Country Blocking** — Block by country code
- **Region Restrictions** — Block specific regions
- **Allowed Countries** — Whitelist approach
- **VPN/Proxy Detection** — Identify anonymizers
- **Tor Exit Node Filtering** — Block known Tor exits

### 5. Traffic Analysis
- **Real-Time Metrics** — Requests/sec, blocked/sec
- **Attack Pattern Recognition** — Identify attack signatures
- **IP Reputation** — Check against threat intel feeds
- **Anomaly Detection** — Unusual traffic patterns
- **Forensics** — Full packet capture (optional)

## API Endpoints

### Protection Rules

#### Create Protection Rule
```http
POST /api/v1/ddos/protection
```

**Request:**
```json
{
  "target": "api.example.com",
  "enabled": true,
  "rules": [
    {
      "type": "rate_limit",
      "threshold": 1000,
      "window": "1m",
      "action": "challenge",
      "exceptions": ["192.168.1.0/24"]
    },
    {
      "type": "geo_block",
      "countries": ["CN", "RU", "KP"],
      "action": "block"
    },
    {
      "type": "challenge",
      "challenge_type": "javascript",
      "difficulty": "medium",
      "threshold": 500
    }
  ],
  "mitigation_mode": "automatic",
  "notification_channels": ["email", "slack"]
}
```

**Response:**
```json
{
  "protection_id": "protect_12345",
  "target": "api.example.com",
  "status": "active",
  "created_at": "2026-05-05T22:00:00Z"
}
```

#### List Protection Rules
```http
GET /api/v1/ddos/protection?target=api.example.com
```

#### Get Protection Details
```http
GET /api/v1/ddos/protection/{protection_id}
```

#### Update Protection
```http
PUT /api/v1/ddos/protection/{protection_id}
```

#### Delete Protection
```http
DELETE /api/v1/ddos/protection/{protection_id}
```

### Metrics

#### Get DDoS Metrics
```http
GET /api/v1/ddos/metrics?target=api.example.com&timeframe=1h
```

**Response:**
```json
{
  "target": "api.example.com",
  "timeframe": "1h",
  "metrics": {
    "total_requests": 150000,
    "blocked_requests": 45000,
    "challenged_requests": 12000,
    "passed_requests": 93000,
    "current_rps": 250,
    "peak_rps": 1200,
    "top_blocked_countries": [
      {"country": "CN", "count": 15000},
      {"country": "RU", "count": 10000}
    ]
  },
  "attacks_detected": 3,
  "mitigation_success_rate": 0.98
}
```

### Attack History

#### List Attacks
```http
GET /api/v1/ddos/attacks?target=api.example.com&limit=10
```

**Response:**
```json
{
  "attacks": [
    {
      "attack_id": "attack_12345",
      "target": "api.example.com",
      "type": "http_flood",
      "peak_rps": 15000,
      "duration_seconds": 300,
      "mitigation_status": "mitigated",
      "started_at": "2026-05-05T20:00:00Z",
      "ended_at": "2026-05-05T20:05:00Z"
    }
  ]
}
```

### Mitigation

#### Trigger Manual Mitigation
```http
POST /api/v1/ddos/mitigate
```

**Request:**
```json
{
  "target": "api.example.com",
  "mitigation_type": "block_ip",
  "ip_address": "192.168.1.100",
  "duration": 3600,
  "reason": "Active attack detected"
}
```

#### Get Challenge Statistics
```http
GET /api/v1/ddos/challenge-stats?timeframe=24h
```

**Response:**
```json
{
  "total_challenges": 5000,
  "passed": 4500,
  "failed": 500,
  "success_rate": 0.9,
  "by_type": {
    "javascript": {"issued": 4000, "passed": 3600},
    "captcha": {"issued": 1000, "passed": 900}
  }
}
```

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Traffic Flow                           │
└──────────────────────────────┬──────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│              Nginx (Reverse Proxy)                       │
│              • Rate limiting (Nginx limit_req)            │
│              • Geofencing (Nginx GeoIP)                 │
└──────────────────────────────┬──────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│          DDoS Protection Service (Port 8021)            │
│          • Advanced rate limiting (Redis)                 │
│          • Challenge-response                            │
│          • Behavioral analysis                           │
└──────────────────────────────┬──────────────────────────┘
                               │
              ┌────────────┴────────────┐
              ▼                        ▼
┌──────────────────┐    ┌──────────────────┐
│  Challenge     │    │  Block/Allow   │
│  Page         │    │  Decision     │
└──────────────────┘    └──────────────────┘
              │                        │
              └────────────┬────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│              Protected Service (e.g., API Gateway)         │
└─────────────────────────────────────────────────────────────┘
```

## Challenge Types

### JavaScript Challenge
```html
<!-- Injected challenge page -->
<!DOCTYPE html>
<html>
<head>
    <script>
        // Solve challenge
        function solveChallenge() {
            const answer = Date.now() + parseInt(document.getElementById('challenge').value);
            window.location.href = window.location.href + '&challenge_answer=' + answer;
        }
    </script>
</head>
<body>
    <h1>Checking your browser...</h1>
    <input type="hidden" id="challenge" value="{{ challenge_value }}" />
    <script>setTimeout(solveChallenge, 3000);</script>
</body>
</html>
```

### CAPTCHA Integration
```python
# challenges/captcha.py
import requests

class CaptchaChallenge:
    def generate(self):
        response = requests.post(
            'https://www.google.com/recaptcha/api/siteverify',
            data={
                'secret': RECAPTCHA_SECRET,
                'response': user_token
            }
        )
        return response.json()['success']
```

## Configuration Examples

### Emergency Mode (Block All)
```json
{
  "target": "api.example.com",
  "emergency_mode": true,
  "allowed_ips": ["192.168.1.0/24"],
  "message": "Under attack, contact security@company.com"
}
```

### API Protection
```json
{
  "target": "api.example.com",
  "rules": [
    {
      "type": "rate_limit",
      "path": "/api/v1/*",
      "threshold": 100,
      "window": "1m",
      "action": "block"
    },
    {
      "type": "rate_limit",
      "path": "/api/v1/auth/*",
      "threshold": 10,
      "window": "1m",
      "action": "challenge"
    }
  ]
}
```

## CLI Usage

```bash
# Create protection rule
cosmicsec ddos create \
  --target api.example.com \
  --rate-limit 1000 \
  --window 1m \
  --geo-block CN,RU

# List protected targets
cosmicsec ddos list

# Get metrics
cosmicsec ddos metrics api.example.com --timeframe 1h

# Trigger mitigation
cosmicsec ddos mitigate api.example.com \
  --type block-ip \
  --ip 192.168.1.100 \
  --duration 3600

# View attack history
cosmicsec ddos attacks api.example.com --limit 10
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Create protection
protection = client.ddos.create(
    target="api.example.com",
    rules=[
        {
            "type": "rate_limit",
            "threshold": 1000,
            "window": "1m",
            "action": "challenge"
        },
        {
            "type": "geo_block",
            "countries": ["CN", "RU"]
        }
    ]
)
print(f"Protection created: {protection.protection_id}")

# Get metrics
metrics = client.ddos.get_metrics(
    target="api.example.com",
    timeframe="1h"
)
print(f"Current RPS: {metrics.metrics['current_rps']}")
print(f"Blocked: {metrics.metrics['blocked_requests']}")

# Trigger mitigation
client.ddos.mitigate(
    target="api.example.com",
    mitigation_type="block_ip",
    ip_address="192.168.1.100",
    duration=3600
)
print("IP blocked for 1 hour")
```

## Monitoring

### Grafana Dashboard
Pre-built dashboard shows:
- **Requests/sec** — Line graph with attack overlay
- **Blocked vs Passed** — Stacked area chart
- **Top Attack Sources** — Bar chart by country/IP
- **Challenge Success Rate** — Gauge chart
- **Mitigation Status** — Status panel

### Prometheus Metrics
- `ddos_requests_total` — Total requests by target
- `ddos_blocked_total` — Blocked requests by rule type
- `ddos_challenges_issued_total` — Challenge types issued
- `ddos_current_rps` — Current requests per second
- `ddos_attacks_active` — Currently active attacks

## Configuration

### Environment Variables
```bash
# Service
DDOS_SERVICE_PORT=8021
DDOS_SERVICE_HOST=0.0.0.0

# Redis (for rate limiting)
REDIS_URL=redis://redis:6379
REDIS_RATE_LIMIT_DB=2

# Nginx Integration
NGINX_CONF_DIR=/etc/nginx/conf.d
NGINX_RELOAD_CMD=nginx -s reload

# CAPTCHA
RECAPTCHA_SITE_KEY=6Lc...
RECAPTCHA_SECRET_KEY=6Lc...

# Geofencing
MAXMIND_LICENSE_KEY=...
GEOIP_DB_PATH=/data/GeoLite2-Country.mmdb

# Notifications
SLACK_WEBHOOK_URL=https://hooks.slack.com/...
NOTIFICATION_EMAIL=security@company.com

# Thresholds
DEFAULT_RATE_LIMIT=1000
DEFAULT_WINDOW=60
MAX_CHALLENGE_DIFFICULTY=5
```

## Testing

### Simulate DDoS Attack (For Testing Only!)
```bash
# Install hping3
sudo apt-get install hping3

# Simulate SYN flood (WARNING: Only test your own systems!)
sudo hping3 -S -p 80 --flood api.example.com

# Using wrk (HTTP flood)
wrk -t12 -c1000 -d60s http://api.example.com/

# Check metrics
curl http://localhost:8021/api/v1/ddos/metrics?target=api.example.com
```

## Troubleshooting

### Legitimate Traffic Being Blocked
```bash
# Check current rate limits
curl http://localhost:8021/api/v1/ddos/protection/protect_12345

# Add whitelist
curl -X PUT http://localhost:8021/api/v1/ddos/protection/protect_12345 \
  -d '{"whitelisted_ips": ["192.168.1.0/24", "10.0.0.0/8"]}'

# Disable specific rule
curl -X PUT http://localhost:8021/api/v1/ddos/protection/protect_12345 \
  -d '{"rules": [{"type": "rate_limit", "enabled": false}]}'
```

### Challenge Page Not Showing
```bash
# Check Nginx config
docker exec nginx nginx -t

# Check challenge endpoint
curl -I http://localhost:8021/challenge

# View logs
docker logs ddos-protection | grep challenge
```

### High False Positive Rate
```bash
# Adjust rate limits (increase threshold)
curl -X PUT http://localhost:8021/api/v1/ddos/protection/protect_12345 \
  -d '{"rules": [{"type": "rate_limit", "threshold": 2000}]}'

# Disable behavioral analysis (if too strict)
export BEHAVIORAL_ANALYSIS_ENABLED=false

# Add legitimate user agents to whitelist
export WHITELIST_USER_AGENTS="Googlebot,Bingbot,..."
```

## Next Steps

- [ZTNA Service](./ztna.md)
- [Threat Intel Feeds](./threat-intel-feeds.md)
- [IoT/OT Security](./iot-ot-security.md)
- [Network Security Guide](../guides/network-security.md)
- [Incident Response Playbooks](../guides/incident-response.md)
