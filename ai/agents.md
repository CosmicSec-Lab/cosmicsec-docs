# Autonomous Agents#

**Autonomous AI Agents** — self-directed security agents.

## Overview#

AI agents that can autonomously investigate, analyze, and respond to security threats.

## Agent Types#

- **Investigation Agent** — deep-dive into alerts#
- **Response Agent** — execute response playbooks#
- **Hunt Agent** — proactive threat hunting#
- **Analysis Agent** — correlate and analyze data#

## Features#

- **Goal-directed behavior** — agents pursue security objectives#
- **Tool usage** — agents use all CosmicSec tools#
- **Colaboration** — multiple agents work together#
- **Human approval** — optional human-in-the-loop#
- **Audit trail** — all actions logged#

## Configuration#

Environment variables:
- `AGENTS_ENABLED` — enable autonomous agents (default: `true`)
- `MAX_AGENT_STEPS` — max steps per agent (default: `50`)
- `HUMAN_APPROVAL_REQUIRED` — require approval (default: `true`)

## API Endpoints#

- `POST /ai/agent/start` — start an autonomous agent#
- `GET /ai/agent/{id}/status` — check agent status#
- `POST /ai/agent/{id}/approve` — approve agent action#
- `GET /ai/agents/logs` — view agent activity logs#

## Usage#

```python
from cosmicsec_ai import AutonomousAgent

agent = AutonomousAgent(agent_type="investigation")
result = agent.run(target="suspicious-domain.com")
```
