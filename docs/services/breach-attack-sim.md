# 🎮 Breach Attack Simulation Service

## Overview

The **Breach Attack Simulation Service** provides automated penetration testing with purple team collaboration. Features MITRE ATT&CK mapping, safe simulation of 50+ attack scenarios, and AI-powered defense recommendations.

**Location:** `cosmicsec-services/services/breach_attack_sim/`  
**Port:** 8026  
**Framework:** FastAPI + Caldera + Atomic Red Team

## Premium Features

### 1. Attack Scenario Library (50+ Scenarios)
| Category | Scenarios | MITRE Tactics |
|----------|-----------|---------------|
| **Initial Access** | Phishing, Exploit Public-Facing App, Hardware Additions | TA0001 |
| **Execution** | PowerShell, CMD, Python, Scripting | TA0002 |
| **Persistence** | Registry Run Keys, Scheduled Tasks, Create Account | TA0003 |
| **Privilege Escalation** | Sudo Caching, Setuid/Setgid, Exploit Kernel | TA0004 |
| **Defense Evasion** | Disabling Security Tools, File Deletion, Obfuscation | TA0005 |
| **Credential Access** | Brute Force, Credential Dumping, Input Capture | TA0006 |
| **Discovery** | Network Service Scanning, Remote System Discovery | TA0007 |
| **Lateral Movement** | Lateral Tool Transfer, Remote Services, SMB/WinRM | TA0008 |
| **Collection** | Data from Information Repositories, Clipboard Data | TA0009 |
| **Command & Control** | Application Layer Protocol, C2 Communication | TA0011 |
| **Exfiltration** | Exfiltration Over C2 Channel, Exfiltration Over Alt Protocol | TA0010 |
| **Impact** | Data Destruction, Ransomware, Stolen Data | TA0040 |

### 2. Purple Team Collaboration
- **Red Team Interface** — Launch attacks, view results
- **Blue Team Interface** — Monitor defenses, practice response
- **Shared Timeline** — Unified view of attack + defense
- **Communication Channel** — Integrated chat
- **Scoring** — Points for detection/mitigation

### 3. Safe Simulation Environment
- **Isolated Network** — No impact to production
- **Mock Data** — Synthetic sensitive data
- **Rate Limiting** — Prevent overload
- **Automatic Cleanup** — Reset after simulation
- **Audit Logging** — Complete attack trail

### 4. MITRE ATT&CK Mapping (Premium)
- **Technique Mapping** — Every attack mapped to ATT&CK
- **Coverage Analysis** — Identify gaps in defenses
- **Heatmap Visualization** — See attack surface
- **Sub-Techniques** — Detailed attribution
- **Threat Actor Emulation** — APT29, Lazarus, etc.

### 5. AI-Powered Defense
- **Defense Recommendations** — AI suggests countermeasures
- **Gap Analysis** — Identify weak defenses
- **Automated Hardening** — Apply security configurations
- **Threat Hunting** — Proactive detection rules
- **Playbook Generation** — Incident response procedures

## API Endpoints

### Create Simulation
```http
POST /api/v1/simulate/scenario
```

**Request:**
```json
{
  "name": "Ransomware Simulation - APT29 Emulation",
  "scenario_id": "ransomware_001",
  "target_network": "sim-network-001",
  "attack_profile": "apt29",  // apt29, lazarus, carbanak, generic
  "start_stage": "initial_access",
  "max_stages": 10,
  "duration_minutes": 120,
  "enable_blue_team": true,
  "purple_team_mode": true,
  "safe_mode": true,
  "notification_channels": ["email", "slack"]
}
```

**Response:**
```json
{
  "simulation_id": "sim_12345",
  "status": "initializing",
  "scenario": "ransomware_001",
  "estimated_duration": 7200,
  "purple_team_url": "http://localhost:8026/purple/sim_12345",
  "created_at": "2026-05-05T22:00:00Z"
}
```

### Get Simulation Status
```http
GET /api/v1/simulate/scenario/{simulation_id}
```

**Response:**
```json
{
  "simulation_id": "sim_12345",
  "scenario": "ransomware_001",
  "status": "in_progress",
  "current_stage": "lateral_movement",
  "completed_stages": ["initial_access", "execution", "persistence"],
  "remaining_stages": ["exfiltration", "impact"],
  "progress": 60,
  "attacks_launched": 15,
  "attacks_detected": 8,
  "attacks_blocked": 5,
  "blue_team_score": 53,  // Detection rate
  "started_at": "2026-05-05T22:00:00Z"
}
```

### Run Simulation
```http
POST /api/v1/simulate/scenario/{simulation_id}/run
```

### Get Simulation Results
```http
GET /api/v1/simulate/scenario/{simulation_id}/results
```

**Response:**
```json
{
  "simulation_id": "sim_12345",
  "scenario": "ransomware_001",
  "summary": {
    "total_attacks": 25,
    "detected": 18,
    "blocked": 12,
    "missed": 7,
    "detection_rate": 0.72,
    "mitigation_rate": 0.48
  },
  "attack_timeline": [
    {
      "timestamp": "2026-05-05T22:05:00Z",
      "stage": "initial_access",
      "technique": "T1566.001",  // Spearphishing Attachment
      "description": "Phishing email sent to HR department",
      "source": "external",
      "target": "user@company.com",
      "detected": true,
      "mitigated": true,
      "blue_team_response_time_seconds": 120
    },
    {
      "timestamp": "2026-05-05T22:15:00Z",
      "stage": "execution",
      "technique": "T1204.002",  // Malicious File
      "description": "User opened malicious Excel attachment",
      "source": "user workstation",
      "target": "hr-workstation-10",
      "detected": false,
      "mitigated": false,
      "impact": "Initial compromise achieved"
    }
  ],
  "mitre_mapping": {
    "T1566.001": {
      "tactic": "Initial Access",
      "name": "Spearphishing Attachment",
      "occurrences": 3,
      "detected": 2,
      "blocked": 1
    },
    "T1059.001": {
      "tactic": "Execution",
      "name": "PowerShell",
      "occurrences": 5,
      "detected": 3,
      "blocked": 2
    }
  },
  "defense_gaps": [
    {
      "area": "Email Security",
      "gap": "Phishing detection rate only 66%",
      "recommendation": "Implement advanced email sandboxing"
    },
    {
      "area": "Endpoint Detection",
      "gap": "PowerShell logging not enabled",
      "recommendation": "Enable PowerShell Script Block Logging"
    }
  ],
  "ai_recommendations": [
    "Deploy EDR solution with behavior-based detection",
    "Implement network segmentation to limit lateral movement",
    "Enable Credential Guard to prevent hash theft",
    "Train users on phishing identification"
  ],
  "purple_team_score": {
    "red_team": 85,   // Attack effectiveness
    "blue_team": 72,  // Defense effectiveness
    "overall": 78
  }
}
```

### List Simulations
```http
GET /api/v1/simulate/scenarios?status=completed&limit=10
```

### Scenario Library
```http
GET /api/v1/simulate/scenarios/library?category=ransomware
```

**Response:**
```json
{
  "scenarios": [
    {
      "id": "ransomware_001",
      "name": "Ransomware Attack Simulation",
      "category": "ransomware",
      "difficulty": "advanced",
      "mitre_tactics": ["TA0001", "TA0002", "TA0008", "TA0010"],
      "estimated_duration_minutes": 120,
      "purple_team_compatible": true
    }
  ]
}
```

### Generate Report
```http
POST /api/v1/simulate/report
```

**Request:**
```json
{
  "simulation_id": "sim_12345",
  "report_type": "executive",  // executive, technical, purple_team
  "format": "pdf",
  "include_timeline": true,
  "include_mitre_heatmap": true,
  "include_defense_gaps": true
}
```

## Attack Scenario: Ransomware Simulation

### Stage 1: Initial Access (T1566.001)
```yaml
- name: "Phishing Email Delivery"
  technique: "T1566.001"
  description: "Send spearphishing email with malicious attachment"
  safe_payload: "Mock ransomware executable (non-malicious)"
  detection_opportunities:
    - Email gateway scanning
    - Attachment sandboxing
    - User reporting
```

### Stage 2: Execution (T1204.002)
```yaml
- name: "Malicious File Execution"
  technique: "T1204.002"
  description: "User opens malicious Excel attachment"
  payload: "Mock macro execution"
  detection_opportunities:
    - PowerShell script logging
    - Process monitoring
    - EDR behavior detection
```

### Stage 3: Persistence (T1547.001)
```yaml
- name: "Registry Run Key"
  technique: "T1547.001"
  description: "Add registry run key for persistence"
  safe_action: "Create mock registry entry"
  detection_opportunities:
    - Registry monitoring
    - Autoruns analysis
```

### Stage 4: Lateral Movement (T1021.001)
```yaml
- name: "RDP Lateral Movement"
  technique: "T1021.001"
  description: "Move laterally via RDP"
  safe_action: "Simulate RDP connection"
  detection_opportunities:
    - Network traffic analysis
    - RDP login monitoring
    - Lateral movement detection rules
```

### Stage 5: Exfiltration (T1041)
```yaml
- name: "Data Exfiltration"
  technique: "T1041"
  description: "Exfiltrate sensitive data"
  safe_payload: "Mock customer database (synthetic data)"
  detection_opportunities:
    - DLP policies
    - Large data transfer alerts
    - Outbound traffic analysis
```

### Stage 6: Impact (T1486)
```yaml
- name: "Data Encrypted for Impact"
  technique: "T1486"
  description: "Ransomware encryption simulation"
  safe_action: "Display ransom note (no actual encryption)"
  detection_opportunities:
    - File integrity monitoring
    - Ransomware behavior detection
    - Backup verification
```

## Purple Team Workflow

### Red Team (Attackers)
1. **Select Scenario** — Choose attack type
2. **Launch Attacks** — Execute attack steps
3. **Evade Detection** — Try to avoid blue team
4. **Achieve Objective** — Complete attack goals

### Blue Team (Defenders)
1. **Monitor Alerts** — SIEM, EDR alerts
2. **Investigate** — Analyze attack indicators
3. **Respond** — Contain and mitigate
4. **Improve Defenses** — Harden systems

### Scoring
```yaml
Blue Team Score:
  Detection Rate: 72%  (18/25 attacks detected)
  Mitigation Rate: 48%  (12/25 attacks mitigated)
  Response Time: Average 4.2 minutes
  Score: 72/100

Red Team Score:
  Evasion Rate: 28%  (7/25 attacks missed)
  Impact Achieved: 85%  (21/25 stages completed)
  Stealth: 65%  (avoided detection in 16/25)
  Score: 85/100
```

## AI Defense Recommendations

### Gap Analysis
```json
{
  "defense_gaps": [
    {
      "area": "Email Security",
      "current_capability": "Basic spam filtering",
      "gap": "No advanced threat protection",
      "recommendation": {
        "action": "Deploy advanced email gateway",
        "solution": "Microsoft Defender for Office 365",
        "cost_estimate": "$2/user/month",
        "implementation_time": "2 weeks"
      }
    },
    {
      "area": "Endpoint Detection",
      "current_capability": "Legacy antivirus",
      "gap": "No behavior-based detection",
      "recommendation": {
        "action": "Deploy EDR solution",
        "solution": "CrowdStrike Falcon or Sentinel One",
        "cost_estimate": "$8/endpoint/month",
        "implementation_time": "4 weeks"
      }
    }
  ]
}
```

### Automated Hardening
```bash
# AI generates hardening script
#!/bin/bash
# Generated by CosmicSec AI

# Enable PowerShell Logging
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" /v EnableScriptBlockLogging /t REG_DWORD /d 1

# Enable Credential Guard
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v LsaCfgFlags /t REG_DWORD /d 1

# Disable LLMNR
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DNSClient" /v EnableMultiCast /t REG_DWORD /d 0

echo "Hardening applied successfully"
```

## CLI Usage

```bash
# List scenarios
cosmicsec sim list-scenarios --category ransomware;

# Create simulation
cosmicsec sim create \
  --scenario ransomware_001 \
  --target sim-network-001 \
  --profile apt29 \
  --purple-team;

# Check status
cosmicsec sim status sim_12345;

# Run simulation
cosmicsec sim run sim_12345;

# Get results
cosmicsec sim results sim_12345 --format json;

# Generate report
cosmicsec sim report \
  --sim sim_12345 \
  --type purple_team \
  --format pdf;

# Stop simulation (emergency)
cosmicsec sim stop sim_12345
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Create simulation
simulation = client.sim.create(
    name="Ransomware Simulation - APT29",
    scenario_id="ransomware_001",
    target_network="sim-network-001",
    attack_profile="apt29",
    purple_team_mode=True,
    safe_mode=True
)

print(f"Simulation created: {simulation.simulation_id}")
print(f"Status: {simulation.status}")

# Wait for completion (with timeout)
import time
while simulation.status not in ["completed", "failed"]:
    simulation = client.sim.get(simulation.simulation_id)
    print(f"Progress: {simulation.progress}%")
    print(f"  Attacks launched: {simulation.attacks_launched}")
    print(f"  Detected: {simulation.attacks_detected}")
    time.sleep(30)

# Get results
results = client.sim.get_results(simulation.simulation_id)

print(f"\n=== Simulation Results ===")
print(f"Detection Rate: {results.summary['detection_rate']*100:.1f}%")
print(f"Blue Team Score: {results.purple_team_score['blue_team']}")

# Print attack timeline
print(f"\n=== Attack Timeline ===")
for attack in results.attack_timeline:
    status = "✅ Detected" if attack['detected'] else "❌ Missed"
    print(f"  {attack['stage']} ({attack['technique']}): {status}")

# AI recommendations
print(f"\n=== AI Recommendations ===")
for rec in results.ai_recommendations:
    print(f"  • {rec}")

# Generate report
report = client.sim.generate_report(
    simulation_id=simulation.simulation_id,
    report_type="purple_team",
    format="pdf"
)
print(f"\nReport generated: {report.download_url}")
```

## Configuration

### Environment Variables
```bash
# Service
BREACH_SIM_PORT=8026
BREACH_SIM_HOST=0.0.0.0

# Simulation Engine
CALDERA_URL=http://caldera:8888
ATOMIC_RED_TEAM_DIR=/opt/atomic-red-team
SAFE_MODE_ENABLED=true
MAX_SIMULTANEOUS_SIMS=5

# Purple Team
PURPLE_TEAM_ENABLED=true
BLUE_TEAM_SCORE_ENABLED=true
RED_TEAM_SCORE_ENABLED=true

# MITRE ATT&CK
ATTACK_NAVIGATOR_URL=https://mitre-attack.github.io/attack-navigator/
ATTACK_DATA_DIR=/app/data/attack

# AI Defense
AI_SERVICE_URL=http://ai-service:8003
AUTO_HARDENING_ENABLED=true

# Notifications
SLACK_WEBHOOK_URL=https://hooks.slack.com/...
NOTIFICATION_EMAIL=security@company.com

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Simulation Network
SIM_NETWORK_CIDR=10.0.0.0/8
SIM_NETWORK_ISOLATED=true
```

## Monitoring

### Metrics
- `simulation_runs_total` — Total simulations by scenario
- `simulation_duration_seconds` — Duration by scenario
- `attacks_launched_total` — Attacks by MITRE technique
- `detection_rate` — Blue team detection rate
- `purple_team_score` — Red vs Blue scores

### Alerts
- Simulation stuck (no progress for 30+ minutes)
- Blue team score below 50% (poor detection)
- Attack escaped simulation environment (critical!)
- Purple team communication failure

## Troubleshooting

### Simulation Fails to Start
```bash
# Check Caldera status
curl http://caldera:8888/api/rest/v2/health

# Check simulation network
docker network ls | grep sim-network

# View logs
docker logs breach-sim | grep "sim_12345"
```

### Attacks Not Running
```bash
# Check Atomic Red Team
ls /opt/atomic-red-team/atomics/

# Verify scenario file
cat /app/scenarios/ransomware_001.yml

# Test payload (safe mode)
docker exec breach-sim python -m tests.test_payload
```

### Blue Team Not Receiving Alerts
```bash
# Check notification channels
curl http://localhost:8026/api/v1/simulate/channels

# Test Slack webhook
curl -X POST $SLACK_WEBHOOK_URL -d '{"text": "Test alert"}'

# Check email delivery
curl -X POST http://localhost:8026/api/v1/simulate/test-notification
```

## Next Steps

- [Edge Computing](./edge-computing.md)
- [SLA Manager](./sla-manager.md)
- [Theme Builder](./theme-builder.md)
- [MITRE ATT&CK Guide](../guides/mitre-attack.md)
- [Purple Team Playbook](../guides/purple-team.md)
