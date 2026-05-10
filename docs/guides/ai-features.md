# AI Features Guide#

**Using Helix AI Features.**

## Overview#

Learn how to use CosmicSec's AI capabilities for security analysis.

## AI Features#

- **Threat Detection** — AI-powered threat identification#
- **Predictive Analytics** — forecast attacks#
- **Automated Remediation** — one-click fixes#
- **Natural Language** — ask questions in plain English#
- **Copilot** — AI assistant for security tasks#

## Using AI Chat#

```python
from cosmicsec_sdk import CosmicSecClient#

client = CosmicSecClient()
response = client.ai.chat("Analyze this threat: ...")
```

## Using Predictive Analytics#

```python
result = client.ai.predict_threats(days_ahead=30)
```

## Using Automated Remediation#

```python
playbook = client.ai.get_remediation(threat_id="...")
```

## Copilot Features#

- **Code analysis** — analyze security code#
- **Playbook generation** — create response playbooks#
- **Report generation** — automated reports#
