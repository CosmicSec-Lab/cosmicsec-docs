# 📊 API Reference — 60+ Endpoints

## Base URLs
- **Production:** `https://api.cosmicsec.com`
- **Staging:** `https://staging-api.cosmicsec.com`
- **Local:** `http://localhost:8000`

## Authentication
All endpoints (except auth) require:
```
Authorization: Bearer <jwt_token>
or
Authorization: ApiKey <api_key>
```

---

## 🔐 Auth Service (Port 8001)

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/register` | Register new user |
| POST | `/api/v1/auth/login` | Login with email/password |
| POST | `/api/v1/auth/refresh` | Refresh JWT token |
| GET | `/api/v1/auth/verify` | Verify email with token |
| POST | `/api/v1/auth/password-reset` | Request password reset |
| POST | `/api/v1/auth/password-reset/confirm` | Confirm password reset |

### 2FA
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/2fa/enable` | Enable 2FA/TOTP |
| POST | `/api/v1/auth/2fa/verify` | Verify 2FA code |
| POST | `/api/v1/auth/2fa/disable` | Disable 2FA |

### OAuth2 & SSO
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/auth/oauth2/{provider}/login` | Initiate OAuth2 |
| GET | `/api/v1/auth/oauth2/{provider}/callback` | OAuth2 callback |
| GET | `/api/v1/auth/saml/login` | Initiate SAML login |
| GET | `/api/v1/auth/saml/callback` | SAML callback |
| GET | `/api/v1/auth/oidc/login` | Initiate OIDC login |

### User Management
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/auth/profile` | Get current user profile |
| PUT | `/api/v1/auth/profile` | Update profile |
| POST | `/api/v1/auth/password/change` | Change password |
| GET | `/api/v1/auth/api-keys` | List API keys |
| POST | `/api/v1/auth/api-keys` | Create API key |
| DELETE | `/api/v1/auth/api-keys/{key_id}` | Revoke API key |

### Admin Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/admin/users` | List all users |
| GET | `/api/v1/admin/users/{user_id}` | Get user details |
| PUT | `/api/v1/admin/users/{user_id}/role` | Update user role |
| DELETE | `/api/v1/admin/users/{identifier}` | Delete user (email or ID) |
| GET | `/api/v1/admin/organizations/{org_id}/users` | List org users |
| GET | `/api/v1/admin/audit/logs` | Get audit logs |

### Settings
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/settings/general` | Get general settings |
| PUT | `/api/v1/settings/general` | Update general settings |

---

## 🔍 Scan Service (Port 8002)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/scans` | Create new scan |
| GET | `/api/v1/scans` | List scans (filter by status) |
| GET | `/api/v1/scans/{scan_id}` | Get scan details |
| DELETE | `/api/v1/scans/{scan_id}` | Cancel/delete scan |
| GET | `/api/v1/scans/{scan_id}/results` | Get scan results |
| GET | `/api/v1/scans/{scan_id}/logs` | Get scan logs |
| GET | `/api/v1/scans/{scan_id}/status` | Get scan status |
| POST | `/api/v1/scans/{scan_id}/report` | Generate report for scan |
| PUT | `/api/v1/scans/{scan_id}/schedule` | Update scan schedule |
| GET | `/api/v1/scans/statistics` | Get scan statistics |

---

## 🤖 AI Service (Port 8003)

### Core Analysis
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Service health check |
| GET | `/ai/models` | List available AI models |
| POST | `/ai/analyze` | Analyze vulnerability/content |
| POST | `/ai/vulnerability/analyze` | Deep vulnerability analysis |
| POST | `/ai/vulnerability/prioritize` | Prioritize vulnerabilities |

### Threat Hunting
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/threat-hunting/query` | NLP threat hunting query |
| POST | `/ai/threat-hunting/analyze` | Analyze threat indicators |
| GET | `/ai/threat-hunting/mitre/{technique_id}` | Get MITRE info |

### MITRE ATT&CK
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/mitre/map` | Map threat to MITRE ATT&CK |
| GET | `/ai/mitre/techniques` | List all techniques |
| GET | `/ai/mitre/tactics` | List all tactics |

### Report Generation
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/report/generate` | Generate AI-powered report |
| POST | `/ai/report/summarize` | Summarize existing report |

### Code Review
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/code/review` | AI code security review |
| POST | `/ai/code/analyze` | Analyze code snippet |

### Detection
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/phishing/detect` | Detect phishing content |
| POST | `/ai/malware/analyze` | Analyze malware sample |
| POST | `/ai/logs/analyze` | Analyze log data |

### Chat & Copilot
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/chat` | Chat with AI assistant |
| WS | `/ai/chat/ws` | WebSocket real-time chat |

### Premium Features
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/ai/ensemble/analyze` | Multi-model ensemble analysis |
| POST | `/ai/learning/feedback` | Submit feedback for learning |
| GET | `/ai/learning/metrics` | Get learning performance |
| POST | `/ai/remediation/generate` | Generate fix playbook |
| POST | `/ai/predictive/datapoint` | Add predictive data point |
| GET | `/ai/predictive/trend` | Get threat trend forecast |

---

## 🌐 Recon Service (Port 8004)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/recon` | Start reconnaissance |
| GET | `/api/v1/recon` | List recon tasks |
| GET | `/api/v1/recon/{recon_id}` | Get recon status |
| DELETE | `/api/v1/recon/{recon_id}` | Cancel recon task |
| GET | `/api/v1/recon/{recon_id}/results` | Get recon results |
| GET | `/api/v1/recon/{recon_id}/subdomains` | Get discovered subdomains |
| GET | `/api/v1/recon/{recon_id}/ports` | Get open ports |
| GET | `/api/v1/recon/{recon_id}/technologies` | Get technology stack |
| GET | `/api/v1/recon/{recon_id}/emails` | Get harvested emails |
| GET | `/api/v1/recon/{recon_id}/cloud` | Get cloud infrastructure info |

---

## 📡 Report Service (Port 8005)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/reports/generate` | Generate new report |
| GET | `/api/v1/reports` | List reports |
| GET | `/api/v1/reports/{report_id}` | Get report details |
| GET | `/api/v1/reports/{report_id}/download` | Download report |
| DELETE | `/api/v1/reports/{report_id}` | Delete report |
| POST | `/api/v1/reports/{report_id}/share` | Share report via email |
| GET | `/api/v1/reports/templates` | List report templates |
| POST | `/api/v1/reports/templates` | Create custom template |

---

## 👥 Collab Service (Port 8006)

| Method | Endpoint | Description |
|--------|----------|-------------|
| WS | `/collab/ws` | WebSocket collaboration |
| POST | `/api/v1/collab/whiteboard` | Create whiteboard |
| GET | `/api/v1/collab/whiteboard/{id}` | Get whiteboard |
| POST | `/api/v1/collab/war-room` | Create war room |
| GET | `/api/v1/collab/war-room/{id}` | Get war room details |
| POST | `/api/v1/collab/notes` | Create shared note |
| GET | `/api/v1/collab/notes/{id}` | Get shared note |
| POST | `/api/v1/collab/action-items` | Create action item |
| GET | `/api/v1/collab/action-items` | List action items |
| PUT | `/api/v1/collab/action-items/{id}` | Update action item |

---

## 🎯 Bug Bounty Service (Port 8007)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/bounty/programs` | Create bounty program |
| GET | `/api/v1/bounty/programs` | List bounty programs |
| GET | `/api/v1/bounty/programs/{id}` | Get program details |
| PUT | `/api/v1/bounty/programs/{id}` | Update program |
| POST | `/api/v1/bounty/submissions` | Submit vulnerability |
| GET | `/api/v1/bounty/submissions` | List submissions |
| GET | `/api/v1/bounty/submissions/{id}` | Get submission |
| PUT | `/api/v1/bounty/submissions/{id}/triage` | Triage submission |
| POST | `/api/v1/bounty/payouts` | Process payout |
| GET | `/api/v1/bounty/leaderboard` | Get researcher leaderboard |

---

## 🛡️ DDoS Protection (Port 8021)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/ddos/protection` | Create protection rule |
| GET | `/api/v1/ddos/protection` | List protection rules |
| GET | `/api/v1/ddos/protection/{id}` | Get protection details |
| PUT | `/api/v1/ddos/protection/{id}` | Update protection |
| GET | `/api/v1/ddos/metrics` | Get DDoS metrics |
| GET | `/api/v1/ddos/attacks` | List DDoS attacks |
| POST | `/api/v1/ddos/mitigate` | Trigger mitigation |
| GET | `/api/v1/ddos/challenge-stats` | Get challenge stats |

---

## 🔐 ZTNA (Port 8022)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/ztna/policies` | Create access policy |
| GET | `/api/v1/ztna/policies` | List access policies |
| GET | `/api/v1/ztna/policies/{id}` | Get policy details |
| PUT | `/api/v1/ztna/policies/{id}` | Update policy |
| DELETE | `/api/v1/ztna/policies/{id}` | Delete policy |
| POST | `/api/v1/ztna/access-request` | Request JIT access |
| GET | `/api/v1/ztna/device-posture` | Check device posture |
| GET | `/api/v1/ztna/sessions` | List active sessions |
| POST | `/api/v1/ztna/mtls/rotate` | Rotate mTLS certs |

---

## 🌐 Threat Intel Feeds (Port 8023)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/threat-intel/feeds` | List available feeds |
| POST | `/api/v1/threat-intel/iocs` | Submit IoC |
| GET | `/api/v1/threat-intel/iocs` | Query IoCs |
| GET | `/api/v1/threat-intel/iocs/{id}` | Get IoC details |
| POST | `/api/v1/threat-intel/import/stix` | Import STIX data |
| GET | `/api/v1/threat-intel/export/stix` | Export as STIX |
| GET | `/api/v1/threat-intel/threats` | List threats |
| GET | `/api/v1/threat-intel/threats/{id}` | Get threat details |
| GET | `/api/v1/threat-intel/actors` | List threat actors |

---

## 📜 Smart Contract Audit (Port 8024)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/audit/contract` | Submit contract for audit |
| GET | `/api/v1/audit/contract/{id}` | Get audit status |
| GET | `/api/v1/audit/contract/{id}/results` | Get audit results |
| POST | `/api/v1/audit/contract/{id}/re-audit` | Re-run audit |
| GET | `/api/v1/audit/supported-languages` | List supported languages |
| GET | `/api/v1/audit/tools` | List audit tools used |

---

## 🎨 3D Visualization (Port 8025)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/visualization/topology` | Get network topology |
| POST | `/api/v1/visualization/node` | Add node to visualization |
| PUT | `/api/v1/visualization/node/{id}` | Update node |
| DELETE | `/api/v1/visualization/node/{id}` | Remove node |
| GET | `/api/v1/visualization/edges` | Get edges/connections |
| POST | `/api/v1/visualization/layout` | Apply layout algorithm |
| GET | `/api/v1/visualization/export` | Export as glTF/PNG |

---

## 🎮 Breach Attack Simulation (Port 8026)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/simulate/scenario` | Create simulation |
| GET | `/api/v1/simulate/scenarios` | List simulations |
| GET | `/api/v1/simulate/scenario/{id}` | Get simulation status |
| POST | `/api/v1/simulate/scenario/{id}/run` | Run simulation |
| GET | `/api/v1/simulate/scenario/{id}/results` | Get results |
| GET | `/api/v1/simulate/scenarios/library` | List scenario library |
| POST | `/api/v1/simulate/report` | Generate simulation report |

---

## 🌐 Edge Computing (Port 8027)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/edge/nodes` | Register edge node |
| GET | `/api/v1/edge/nodes` | List edge nodes |
| GET | `/api/v1/edge/nodes/{id}` | Get node details |
| PUT | `/api/v1/edge/nodes/{id}` | Update node config |
| DELETE | `/api/v1/edge/nodes/{id}` | Deregister node |
| GET | `/api/v1/edge/metrics` | Get edge metrics |
| POST | `/api/v1/edge/deploy` | Deploy to edge |
| GET | `/api/v1/edge/topology` | Get fog topology |

---

## 📊 SLA Manager (Port 8028)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/sla/tiers` | List SLA tiers |
| POST | `/api/v1/sla/subscribe` | Subscribe to tier |
| GET | `/api/v1/sla/status` | Get SLA compliance |
| GET | `/api/v1/sla/metrics` | Get SLA metrics |
| GET | `/api/v1/sla/penalties` | List penalty events |
| POST | `/api/v1/sla/penalties/dispute` | Dispute penalty |

---

## 🎨 Theme Builder (Port 8029)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/themes/presets` | List preset themes |
| POST | `/api/v1/themes` | Create custom theme |
| GET | `/api/v1/themes` | List custom themes |
| GET | `/api/v1/themes/{id}` | Get theme details |
| PUT | `/api/v1/themes/{id}` | Update theme |
| DELETE | `/api/v1/themes/{id}` | Delete theme |
| POST | `/api/v1/themes/{id}/apply` | Apply theme |
| POST | `/api/v1/themes/import` | Import theme JSON |
| GET | `/api/v1/themes/export/{id}` | Export theme JSON |

---

## 🚀 Onboarding Wizard (Port 8030)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/onboarding/steps` | Get onboarding steps |
| POST | `/api/v1/onboarding/step/{step}/complete` | Complete step |
| GET | `/api/v1/onboarding/status` | Get onboarding status |
| POST | `/api/v1/onboarding/skip` | Skip onboarding |
| POST | `/api/v1/onboarding/reset` | Reset onboarding |

---

## 🤖 NLP Search (Port 8031)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/nlp/search` | Natural language search |
| GET | `/api/v1/nlp/intents` | List recognized intents |
| GET | `/api/v1/nlp/entities` | List extracted entities |
| POST | `/api/v1/nlp/feedback` | Submit search feedback |
| GET | `/api/v1/nlp/suggestions` | Get search suggestions |

---

## 🔔 Notification Service (Port 8011)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/notifications` | List notifications |
| PUT | `/api/v1/notifications/{id}/read` | Mark as read |
| PUT | `/api/v1/notifications/read-all` | Mark all as read |
| GET | `/api/v1/notifications/preferences` | Get preferences |
| PUT | `/api/v1/notifications/preferences` | Update preferences |
| POST | `/api/v1/notifications/test` | Send test notification |
| GET | `/api/v1/notifications/channels` | List channels |

---

## 📝 Compliance Service (Port 8012)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/compliance/standards` | List compliance standards |
| POST | `/api/v1/compliance/audit` | Run compliance audit |
| GET | `/api/v1/compliance/audit/{id}` | Get audit results |
| GET | `/api/v1/compliance/status` | Get compliance status |
| POST | `/api/v1/compliance/report` | Generate compliance report |
| GET | `/api/v1/compliance/controls` | List control status |

---

## 👥 Org Service (Port 8013)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/org` | Get organization details |
| PUT | `/api/v1/org` | Update organization |
| POST | `/api/v1/org/invite` | Invite user |
| GET | `/api/v1/org/members` | List members |
| PUT | `/api/v1/org/members/{id}/role` | Update member role |
| DELETE | `/api/v1/org/members/{id}` | Remove member |
| GET | `/api/v1/org/billing` | Get billing info |
| PUT | `/api/v1/org/billing` | Update billing |

---

## 🔐 Admin Service (Port 8014)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/admin/dashboard` | Admin dashboard stats |
| GET | `/api/v1/admin/system/health` | System health |
| GET | `/api/v1/admin/system/metrics` | System metrics |
| POST | `/api/v1/admin/maintenance/mode` | Toggle maintenance |
| GET | `/api/v1/admin/logs` | Get system logs |
| PUT | `/api/v1/admin/settings` | Update system settings |
| POST | `/api/v1/admin/backup` | Trigger backup |
| GET | `/api/v1/admin/backup/status` | Get backup status |

---

## 🚪 Egress Service (Port 8015)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/egress/rules` | List egress rules |
| POST | `/api/v1/egress/rules` | Create egress rule |
| PUT | `/api/v1/egress/rules/{id}` | Update egress rule |
| DELETE | `/api/v1/egress/rules/{id}` | Delete egress rule |
| GET | `/api/v1/egress/metrics` | Get egress metrics |
| GET | `/api/v1/egress/logs` | Get egress traffic logs |

---

## 🤖 Ingest Service (Port 8016) — Rust

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/ingest/parse` | Parse tool output |
| GET | `/api/v1/ingest/formats` | List supported formats |
| POST | `/api/v1/ingest/batch` | Batch parse |
| GET | `/api/v1/ingest/metrics` | Get parser metrics |
| gRPC | `Ingest.Parse` | High-performance gRPC parsing |

---

## 🌐 DeepIntel PRO (Port 8032)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/intel/search` | Search dark web intel |
| GET | `/api/v1/intel/alerts` | List alert rules |
| POST | `/api/v1/intel/alerts` | Create alert rule |
| PUT | `/api/v1/intel/alerts/{id}` | Update alert rule |
| DELETE | `/api/v1/intel/alerts/{id}` | Delete alert rule |
| POST | `/api/v1/intel/export/stix` | Export as STIX |
| GET | `/api/v1/intel/networks/status` | Get network status |
| GET | `/api/v1/intel/crawlers/status` | Get crawler status |
| POST | `/api/v1/intel/proxy/rotate` | Rotate proxy |

---

## GraphQL Federation (Port 8000)

### Endpoint
```
POST /graphql
```

### Example Query
```graphql
query {
  scans {
    scanId
    target
    status
    vulnerabilities {
      title
      severity
      cve
    }
  }
  
  threats {
    threatId
    type
    severity
    mitreMapping {
      techniqueId
      tactic
    }
  }
  
  reports {
    reportId
    createdAt
    downloadUrl
  }
}
```

### Example Mutation
```graphql
mutation {
  createScan(input: {
    target: "example.com",
    scanType: "web_app",
    tools: ["nmap", "nuclei"]
  }) {
    scanId
    status
  }
}
```

---

## Response Format

### Success Response
```json
{
  "data": { ... },
  "message": "Operation successful",
  "timestamp": "2026-05-05T22:00:00Z"
}
```

### Error Response
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Scan with ID scan_12345 not found",
    "details": {
      "resource": "scan",
      "id": "scan_12345"
    }
  },
  "timestamp": "2026-05-05T22:00:00Z"
}
```

### Pagination
```json
{
  "data": [ ... ],
  "pagination": {
    "total": 100,
    "limit": 10,
    "offset": 0,
    "has_more": true
  }
}
```

---

## Rate Limiting Headers

All responses include rate limit info:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1620000360
```

---

## Next Steps

- [Service Documentation](./services/)
- [SDK Usage](./sdks/)
- [CLI Commands](./cli/commands.md)
- [GraphQL Schema](./dev/graphql-schema.md)
