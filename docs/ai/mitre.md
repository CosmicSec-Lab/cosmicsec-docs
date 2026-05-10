# MITRE ATT&CK Mapping#

**MITRE ATT&CK Module** — map threats to attack patterns.

## Overview#

Automatically maps detected threats to MITRE ATT&CK framework techniques and tactics.

## Features#

- **Automatic mapping** — map threats to technique IDs#
- **Coverage analysis** — identify gaps in detection#
- **Heatmap generation** — visualize attack surface#
- **Threat actor profiling** — link to known groups#
- **Mitigation suggestions** — get remediation guidance#

## Supported Techniques#

- **T1566** — Phishing#
- **T1059** — Command and Scripting Interpreter#
- **T1078** — Valid Accounts#
- **T1053** — Scheduled Task/Job#
- **T1027** — Obfuscated Files or Information#

## Configuration#

Environment variables:
- `MITRE_ENABLED` — enable ATT&CK mapping (default: `true`)
- `MAP_CONFIDENCE_THRESHOLD` — minimum confidence (default: `0.7`)
- `AUTO_MITIGATE` — automatically suggest mitigations (default: `true`)

## API Endpoints#

- `POST /ai/map-attack` — map threat to ATT&CK technique#
- `GET /ai/attack-coverage` — get coverage report#
- `GET /ai/technique/{id}` — get technique details#
- `POST /ai/mitigate` — get mitigation steps#

## Usage#

```python
from cosmicsec_ai import MITREMapper

mapper = MITREMapper()
technique = mapper.map_threat(threat_description)
```
