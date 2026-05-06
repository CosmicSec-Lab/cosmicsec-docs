# 🔔 Notification Service

## Overview

The **Notification Service** provides multi-channel alerts with smart batching, escalation policies, and premium delivery guarantees.

**Location:** `cosmicsec-services/services/notification_service/`  
**Port:** 8011  
**Framework:** FastAPI + Redis Pub/Sub + WebSockets

## Premium Features

### 1. Multi-Channel Delivery (12+ Channels)
| Channel | Type | Description |
|---------|------|-------------|
| **Email** | Async | SMTP, SendGrid, SES, Mailgun |
| **Slack** | Webhook | Rich messages with blocks |
| **Microsoft Teams** | Webhook | Adaptive cards |
| **Discord** | Webhook | Embeds with colors |
| **Webhook** | HTTP | Custom endpoint |
| **SMS** | Sync | Twilio, AWS SNS |
| **Push** | WebSocket | Browser/mobile push |
| **Telegram** | Bot API | Bot messages |
| **PagerDuty** | API | Incident management |
| **Opsgenie** | API | Alert routing |
| **VictorOps** | API | Incident response |
| **Custom** | Plugin | Your own channel |

### 2. Smart Batching
- **Time Window** — Batch within 5min window
- **Count Threshold** — Send after 10 notifications
- **Priority Override** — Critical bypasses batch
- **Digest Mode** — Daily/weekly summaries

### 3. Escalation Policies
```
Level 1: Email → Wait 15min → Level 2: SMS → Wait 30min → Level 3: PagerDuty
```

### 4. Notification Preferences (Per-User)
- **Channel Selection** — Choose preferred channels
- **Quiet Hours** — Do not disturb (10 PM - 8 AM)
- **Severity Filtering** — Only critical/high
- **Category Subscriptions** — Vulns, compliance, SLA

### 5. Delivery Guarantees
- **At-Least-Once** — Retry until acknowledged
- **Deduplication** — Same notification only once per window
- **Rate Limiting** — Max 10 emails/hour/user
- **Falback Channels** — Email fails → Try SMS

## API Endpoints

### List Notifications
```http
GET /api/v1/notifications?status=unread&limit=20&offset=0
```

**Response:**
```json
{
  "notifications": [
    {
      "notification_id": "notif_12345",
      "type": "vulnerability_found",
      "severity": "critical",
      "title": "Critical SQL Injection Found",
      "message": "Scan scan_12345 found critical SQL injection...",
      "channels": ["email", "slack"],
      "status": "delivered",
      "created_at": "2026-05-05T22:00:00Z",
      "delivered_at": "2026-05-05T22:00:05Z",
      "read": false,
      "metadata": {
        "scan_id": "scan_12345",
        "vuln_id": "vuln_001"
      }
    }
  ],
  "total": 45,
  "unread_count": 12,
  "limit": 20,
  "offset": 0
}
```

### Mark as Read
```http
PUT /api/v1/notifications/{notification_id}/read
```

### Mark All as Read
```http
PUT /api/v1/notifications/read-all
```

### Get Preferences
```http
GET /api/v1/notifications/preferences
```

**Response:**
```json
{
  "user_id": "user_123",
  "channels": {
    "email": {
      "enabled": true,
      "address": "user@company.com",
      "verified": true
    },
    "slack": {
      "enabled": true,
      "webhook_url": "https://hooks.slack.com/...",
      "channel": "#security"
    }
  },
  "quiet_hours": {
    "enabled": true,
    "start": "22:00",
    "end": "08:00",
    "timezone": "America/New_York"
  },
  "severity_filter": ["critical", "high"],
  "categories": ["vulnerabilities", "compliance"],
  "digest_mode": {
    "enabled": false,
    "frequency": "daily"  // daily, weekly
  }
}
```

### Update Preferences
```http
PUT /api/v1/notifications/preferences
```

**Request:**
```json
{
  "channels": {
    "email": {"enabled": true},
    "slack": {"enabled": false}
  },
  "quiet_hours": {
    "enabled": true,
    "start": "23:00",
    "end": "07:00"
  },
  "severity_filter": ["critical"]
}
```

### Send Test Notification
```http
POST /api/v1/notifications/test
```

**Request:**
```json
{
  "channels": ["email", "slack"],
  "message": "Test notification from CosmicSec"
}
```

### Get Channels
```http
GET /api/v1/notifications/channels
```

**Response:**
```json
{
  "channels": [
    {
      "id": "email",
      "name": "Email",
      "type": "async",
      "enabled": true,
      "configured": true
    },
    {
      "id": "slack",
      "name": "Slack",
      "type": "webhook",
      "enabled": true,
      "configured": true
    }
  ]
}
```

## WebSocket Real-Time Notifications

### Connect to Notifications
```javascript
const ws = new WebSocket('ws://localhost:8011/notifications/ws');

ws.onopen = () => {
  // Subscribe to notifications
  ws.send(JSON.stringify({
    type: 'subscribe',
    user_id: 'user_123',
    channels: ['email', 'slack']
  }));
};

ws.onmessage = (event) => {
  const notification = JSON.parse(event.data);
  
  // Show toast notification
  showToast({
    title: notification.title,
    message: notification.message,
    severity: notification.severity,
    onClick: () => {
      // Navigate to related resource
      window.location.href = notification.metadata?.url || '/notifications';
    }
  });
};

// Mark as read
function markAsRead(notificationId) {
  ws.send(JSON.stringify({
    type: 'mark_read',
    notification_id: notificationId
  }));
}
```

## Notification Types

### Vulnerability Found
```json
{
  "type": "vulnerability_found",
  "severity": "critical",
  "title": "Critical Vulnerability Detected",
  "message": "SQL injection found in login form",
  "metadata": {
    "scan_id": "scan_12345",
    "vuln_id": "vuln_001",
    "cve": "CVE-2024-12345",
    "cvss_score": 9.8
  }
}
```

### Scan Completed
```json
{
  "type": "scan_completed",
  "severity": "info",
  "title": "Scan Completed",
  "message": "Scan scan_12345 found 15 vulnerabilities",
  "metadata": {
    "scan_id": "scan_12345",
    "status": "completed",
    "vulnerabilities_count": 15,
    "report_id": "report_12345"
  }
}
```

### SLA Breach
```json
{
  "type": "sla_breach",
  "severity": "high",
  "title": "SLA Breach Detected",
  "message": "API Gateway uptime below 99.9%",
  "metadata": {
    "sla_id": "sla_12345",
    "service": "api-gateway",
    "guaranteed": 99.9,
    "actual": 99.85,
    "penalty": 150.00
  }
}
```

## CLI Usage

```bash
# List notifications
cosmicsec notifications list --status unread --limit 20

# Mark as read
cosmicsec notifications read notif_12345

# Mark all as read
cosmicsec notifications read-all

# Get preferences
cosmicsec notifications preferences get

# Update preferences
cosmicsec notifications preferences set \
  --channels email,slack \
  --quiet-hours "22:00-08:00" \
  --severity-filter critical,high

# Send test
cosmicsec notifications test \
  --channels email,slack \
  --message "Test notification"
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# List notifications
notifications = client.notifications.list(
    status='unread',
    limit=20
)

print(f"Unread: {len(notifications.notifications)}")
for notif in notifications.notifications:
    print(f"  [{notif.severity.upper()}] {notif.title}")

# Mark as read
client.notifications.mark_read(notification_id='notif_12345')
print("Marked as read")

# Get preferences
prefs = client.notifications.get_preferences()
print(f"\nChannels:")
for channel, config in prefs.channels.items():
    status = "✅" if config['enabled'] else "❌"
    print(f"  {status} {channel}")

# Update preferences
client.notifications.update_preferences(
    channels={
        'email': {'enabled': True},
        'slack': {'enabled': False}
    },
    quiet_hours={
        'enabled': True,
        'start': '23:00',
        'end': '07:00'
    },
    severity_filter=['critical']
)
print("Preferences updated!")

# Send test
client.notifications.send_test(
    channels=['email', 'slack'],
    message="Test from CosmicSec"
)
```

## Configuration

### Environment Variables
```bash
# Service
NOTIFICATION_SERVICE_PORT=8011
NOTIFICATION_SERVICE_HOST=0.0.0.0

# Email (SMTP)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=user@gmail.com
SMTP_PASS=app_password

# Slack
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/...

# Microsoft Teams
TEAMS_WEBHOOK_URL=https://outlook.office.com/webhook/...

# Twilio (SMS)
TWILIO_ACCOUNT_SID=AC...
TWILIO_AUTH_TOKEN=...
TWILIO_FROM_NUMBER=+1234567890

# WebSockets
WS_HEARTBEAT_INTERVAL=30
WS_MAX_CONNECTIONS=10000

# Batching
BATCH_ENABLED=true
BATCH_WINDOW_SECONDS=300
BATCH_MAX_COUNT=10

# Escalation
ESCALATION_ENABLED=true
ESCALATION_TIMEOUT_SECONDS=900

# Redis (for queuing)
REDIS_URL=redis://redis:6379

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Troubleshooting

### Emails Not Sending
```bash
# Check SMTP connectivity
telnet smtp.gmail.com 587

# Check logs
docker logs notification-service | grep "smtp"

# Test email
curl -X POST http://localhost:8011/api/v1/notifications/test \
  -d '{"channels": ["email"], "message": "test"}'
```

### Slack Notifications Failing
```bash
# Verify webhook URL
curl -X POST $SLACK_WEBHOOK_URL \
  -H "Content-type: application/json" \
  -d '{"text": "test"}'

# Check logs
docker logs notification-service | grep "slack"
```

## Next Steps

- [Compliance Service](./compliance-service.md)
- [Org Service](./org-service.md)
- [Admin Service](./admin-service.md)
- [Egress Service](./egress-service.md)
- [Notification Guide](../guides/notifications.md)
