# 🚪 ZTNA — Zero-Trust Network Access

## Overview

The **ZTNA Service** implements a Zero-Trust architecture where no user or device is trusted by default. Features mTLS, device posture checks, micro-segmentation, and Just-In-Time (JIT) access.

**Location:** `cosmicsec-services/services/ztna/`  
**Port:** 8022  
**Framework:** FastAPI + WireGuard + mTLS

## Features

### 1. Zero-Trust Principles
- **Never Trust, Always Verify** — Every access request authenticated
- **Least Privilege Access** — Minimal necessary permissions
- **Micro-Segmentation** — Per-resource access control
- **Continuous Verification** — Re-authenticate periodically
- **Device Posture Checks** — OS version, patches, antivirus

### 2. mTLS (Mutual TLS)
- **Certificate-Based Auth** — Both client and server authenticate
- **Automatic Cert Rotation** — 90-day cert lifecycle
- **Certificate Revocation** — CRL/OCSP support
- **PKI Integration** — Your existing PKI or built-in CA
- **Quantum-Safe Options** — Hybrid classical+post-quantum

### 3. Device Posture
- **OS Version Check** — Minimum OS requirements
- **Patch Level** — Required security updates
- **Antivirus Status** — Active & up-to-date
- **Firewall Enabled** — Host firewall check
- **Disk Encryption** — Full disk encryption required
- **MDM Integration** — Intune, Jamf, Workspace ONE

### 4. Just-In-Time Access
- **Temporary Access** — Time-bound permissions
- **Approval Workflow** — Manager/peer approval
- **Emergency Access** — Break-glass procedures
- **Session Recording** — Full session capture
- **Automatic Revocation** — Access expires automatically

### 5. Micro-Segmentation
- **Per-Resource Policies** — Unique policy per resource
- **Dynamic Policies** — Context-aware decisions
- **Time-Based Access** — Business hours only
- **Location-Based** — Office vs remote
- **Risk-Adaptive** — Higher risk = more restrictions

## API Endpoints

### Access Policies

#### Create Policy
```http
POST /api/v1/ztna/policies
```

**Request:**
```json
{
  "name": "Developer Access to Staging",
  "description": "Developers can access staging APIs during work hours",
  "resources": ["staging-api.example.com", "staging-db.example.com"],
  "conditions": {
    "user_roles": ["developer", "devops"],
    "device_posture": {
      "os": "updated",
      "antivirus": "active",
      "disk_encryption": true
    },
    "time_window": "9:00-17:00",
    "days": ["mon", "tue", "wed", "thu", "fri"],
    "locations": ["office", "vpn"],
    "mfa_required": true,
    "max_risk_score": 30
  },
  "session": {
    "duration": 3600,
    "reauth_interval": 1800,
    "recording_enabled": false
  }
}
```

**Response:**
```json
{
  "policy_id": "pol_12345",
  "name": "Developer Access to Staging",
  "status": "active",
  "created_at": "2026-05-05T22:00:00Z"
}
```

#### List Policies
```http
GET /api/v1/ztna/policies?status=active&limit=10
```

#### Get Policy
```http
GET /api/v1/ztna/policies/{policy_id}
```

#### Update Policy
```http
PUT /api/v1/ztna/policies/{policy_id}
```

#### Delete Policy
```http
DELETE /api/v1/ztna/policies/{policy_id}
```

### Access Requests

#### Request JIT Access
```http
POST /api/v1/ztna/access-request
```

**Request:**
```json
{
  "policy_id": "pol_12345",
  "resource": "staging-api.example.com",
  "duration": 3600,
  "reason": "Deploying hotfix for login bug",
  "approver": "manager_123"
}
```

**Response:**
```json
{
  "request_id": "req_12345",
  "status": "pending_approval",
  "estimated_approval_time": 300,
  "approver_notified": true
}
```

#### Check Access
```http
GET /api/v1/ztna/check-access?resource=staging-api.example.com&user=user_123
```

**Response:**
```json
{
  "access_granted": true,
  "policy_id": "pol_12345",
  "session_token": "ztna_token_...",
  "expires_at": "2026-05-05T23:00:00Z",
  "conditions_met": {
    "user_role": true,
    "device_posture": true,
    "time_window": true,
    "mfa": true
  }
}
```

### Device Posture

#### Report Posture
```http
POST /api/v1/ztna/device-posture
```

**Request:**
```json
{
  "device_id": "device_12345",
  "user_id": "user_123",
  "os": {
    "type": "windows",
    "version": "10.0.19045",
    "patch_level": "2026-05",
    "is_updated": true
  },
  "security": {
    "antivirus": {
      "product": "Windows Defender",
      "status": "active",
      "definitions_updated": "2026-05-05"
    },
    "firewall": {
      "enabled": true,
      "type": "windows_firewall"
    },
    "disk_encryption": {
      "enabled": true,
      "type": "bitlocker"
    }
  },
  "mdm": {
    "enrolled": true,
    "compliant": true
  }
}
```

#### Get Device Posture
```http
GET /api/v1/ztna/device-posture/{device_id}
```

### mTLS Certificates

#### Enroll Device
```http
POST /api/v1/ztna/mtls/enroll
```

**Request:**
```json
{
  "device_id": "device_12345",
  "csr": "-----BEGIN CERTIFICATE REQUEST-----\n...",
  "validity_days": 90
}
```

**Response:**
```json
{
  "certificate": "-----BEGIN CERTIFICATE-----\n...",
  "ca_chain": "-----BEGIN CERTIFICATE-----\n...",
  "expires_at": "2026-08-03T22:00:00Z"
}
```

#### Rotate Certificate
```http
POST /api/v1/ztna/mtls/rotate
```

#### Revoke Certificate
```http
POST /api/v1/ztna/mtls/revoke
```

### Active Sessions

#### List Sessions
```http
GET /api/v1/ztna/sessions?user_id=user_123&active_only=true
```

**Response:**
```json
{
  "sessions": [
    {
      "session_id": "sess_12345",
      "user_id": "user_123",
      "device_id": "device_12345",
      "resource": "staging-api.example.com",
      "started_at": "2026-05-05T22:00:00Z",
      "expires_at": "2026-05-05T23:00:00Z",
      "recording_url": "https://..."
    }
  ]
}
```

#### Terminate Session
```http
DELETE /api/v1/ztna/sessions/{session_id}
```

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    User Device                              │
│  • ZTNA Agent (background service)                      │
│  • Certificate Store (mTLS cert)                        │
│  • Posture Collector                                 │
└──────────────────────────────┬──────────────────────────┘
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
            ┌─────────────┐    ┌─────────────┐
            │  mTLS      │    │  Posture   │
            │  Handshake │    │  Report    │
            └─────────────┘    └─────────────┘
                    │                     │
                    └──────────┬──────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                  ZTNA Gateway                            │
│  • Policy Engine (authorization)                        │
│  • mTLS Termination                                   │
│  • Session Management                                 │
└──────────────────────────────┬──────────────────────────┘
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
            ┌─────────────┐    ┌─────────────┐
            │  Allow     │    │  Deny      │
            │  Access    │    │  (403)     │
            └─────────────┘    └─────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────────┐
│                  Protected Resource                        │
│  • staging-api.example.com                             │
│  • staging-db.example.com                              │
└─────────────────────────────────────────────────────────────┘
```

## Policy Evaluation Flow

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant D as 📱 Device
    participant Z as 🔐 ZTNA Gateway
    participant P as 📜 Policy Engine
    
    U->>D: Request Access to Resource
    D->>Z: mTLS Handshake + Posture Report
    Z->>P: Evaluate Policy
    P->>P: Check User Role
    P->>P: Check Device Posture
    P->>P: Check Time Window
    P->>P: Check MFA Status
    P->>Z: Decision (Allow/Deny)
    
    alt Access Granted
        Z->>D: Issue Session Token
        D->>U: Access Granted (200 OK)
    else Access Denied
        Z->>D: 403 Forbidden
        D->>U: Access Denied (Reason)
    end
```

## ZTNA Agent (Client-Side)

### Installation
```bash
# Windows
msiexec /i CosmicSec-ZTNA-Agent.msi /quiet

# macOS
brew install cosmicsec-ztna-agent

# Linux
sudo apt-get install cosmicsec-ztna-agent
```

### Configuration
```json
{
  "ztna_gateway": "ztna.example.com:8022",
  "certificate_path": "~/.cosmicsec/ztna.crt",
  "private_key_path": "~/.cosmicsec/ztna.key",
  "posture_reporting_interval": 300,
  "auto_rotate_certs": true
}
```

### Agent Commands
```bash
# Check status
cosmicsec-ztna status

# Enroll device
cosmicsec-ztna enroll --device-id device_12345

# Report posture
cosmicsec-ztna report-posture

# Request access
cosmicsec-ztna request-access \
  --resource staging-api.example.com \
  --duration 3600 \
  --reason "Deploying hotfix"
```

## CLI Usage

```bash
# Create policy
cosmicsec ztna policy create \
  --name "Developer Access" \
  --resources staging-api.example.com \
  --roles developer,devops \
  --mfa-required

# List policies
cosmicsec ztna policy list --status active

# Request JIT access
cosmicsec ztna access request \
  --policy pol_12345 \
  --resource staging-api.example.com \
  --duration 3600

# Check access
cosmicsec ztna check-access \
  --resource staging-api.example.com \
  --user user_123

# Enroll device
cosmicsec ztna mtls enroll \
  --device device_12345 \
  --validity 90

# Rotate certificates
cosmicsec ztna mtls rotate --device device_12345

# List active sessions
cosmicsec ztna sessions list --user user_123
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Create policy
policy = client.ztna.create_policy(
    name="Developer Access to Staging",
    resources=["staging-api.example.com"],
    conditions={
        "user_roles": ["developer"],
        "device_posture": {"antivirus": "active"},
        "time_window": "9:00-17:00",
        "mfa_required": True
    }
)
print(f"Policy created: {policy.policy_id}")

# Request JIT access
access = client.ztna.request_access(
    policy_id=policy.policy_id,
    resource="staging-api.example.com",
    duration=3600,
    reason="Hotfix deployment"
)
print(f"Access status: {access.status}")

# Check access
check = client.ztna.check_access(
    resource="staging-api.example.com",
    user_id="user_123"
)
if check.access_granted:
    print(f"Access granted until {check.expires_at}")
else:
    print("Access denied")

# Enroll device
cert = client.ztna.enroll_device(
    device_id="device_12345",
    validity_days=90
)
print(f"Certificate issued, expires: {cert.expires_at}")
```

## Integration with SSO

### SAML 2.0 Flow
```
1. User logs in via SSO (SAML assertion)
2. ZTNA extracts user role from SAML attributes
3. Policy engine checks if role is allowed
4. Device posture verified
5. Session token issued
```

### OIDC Integration
```python
# Configuration
ZTNA_SSO = {
    "provider": "keycloak",
    "issuer": "https://keycloak.company.com/realms/master",
    "client_id": "...",
    "attribute_mapping": {
        "role": "roles",
        "email": "email",
        "user_id": "sub"
    }
}
```

## Monitoring

### Metrics
- `ztna_access_requests_total` — Total access requests by decision
- `ztna_active_sessions` — Current active sessions
- `ztna_policy_evaluations_total` — Policy checks by result
- `ztna_device_posture_failures_total` — Posture check failures
- `ztna_certificates_expiring` — Certs expiring within 7 days

### Dashboard
Pre-built Grafana dashboard shows:
- **Access Requests** — Allowed vs Denied (pie chart)
- **Policy Evaluation** — Evaluation time histogram
- **Device Posture** — Pass/fail by check type
- **Active Sessions** — Gauge with expiry timeline
- **Certificate Status** — Expiry countdown

## Configuration

### Environment Variables
```bash
# Service
ZTNA_SERVICE_PORT=8022
ZTNA_SERVICE_HOST=0.0.0.0

# mTLS
ZTNA_CA_KEY=path/to/ca.key
ZTNA_CA_CERT=path/to/ca.crt
CERT_VALIDITY_DAYS=90
AUTO_ROTATE_CERTS=true

# Policy Engine
POLICY_EVALUATION_TIMEOUT=5
DEFAULT_DENY_ACTION=true
MAX_CONCURRENT_SESSIONS=10000

# Device Posture
POSTURE_CHECK_INTERVAL=300
REQUIRE_DISK_ENCRYPTION=true
REQUIRE_ANTIVIRUS=true
ALLOWED_OS_VERSIONS=windows:10.0+,macos:12.0+,linux:5.0+

# Session Management
DEFAULT_SESSION_DURATION=3600
MAX_SESSION_DURATION=86400
SESSION_RECORDING_ENABLED=false

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Redis (session store)
REDIS_URL=redis://redis:6379
```

## Security Best Practices

### 1. Principle of Least Privilege
- Grant minimum necessary access
- Use JIT for privileged access
- Regularly audit permissions

### 2. Defense in Depth
- mTLS + Posture + Policy + MFA
- Layer security controls

### 3. Continuous Verification
- Re-check posture periodically
- Short session durations
- Re-authenticate for sensitive actions

### 4. Audit Everything
- Log all access decisions
- Record privileged sessions
- Regular access reviews

## Troubleshooting

### Access Denied
```bash
# Check policy
curl http://localhost:8022/api/v1/ztna/policies/pol_12345

# Check device posture
curl http://localhost:8022/api/v1/ztna/device-posture/device_12345

# Test policy evaluation
curl http://localhost:8022/api/v1/ztna/check-access \
  -d '{"resource": "staging-api.example.com", "user_id": "user_123"}'
```

### Certificate Issues
```bash
# Check certificate expiry
openssl x509 -in device.crt -text -noout | grep "Not After"

# Check certificate revocation
openssl verify -CAfile ca.crt device.crt

# Re-enroll device
cosmicsec ztna mtls enroll --device device_12345 --force
```

### Session Not Working
```bash
# List active sessions
curl http://localhost:8022/api/v1/ztna/sessions?active_only=true

# Check session token validity
jwt decode <session_token>

# Terminate and retry
curl -X DELETE http://localhost:8022/api/v1/ztna/sessions/sess_12345
```

## Next Steps

- [IoT/OT Security](./iot-ot-security.md)
- [DDoS Protection](./ddos-protection.md)
- [Threat Intel Feeds](./threat-intel-feeds.md)
- [Compliance Service](./compliance-service.md)
- [Zero-Trust Guide](../guides/zero-trust.md)
