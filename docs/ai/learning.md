# Real-Time Learning#

**Real-Time Learning Module** — continuous AI adaptation.

## Overview#

Enables AI models to learn from security analysts' feedback in real-time.

## Features#

- **Feedback loops** — analysts rate AI suggestions#
- **Automatic model adjustment** — models adapt to your environment#
- **Performance tracking** — per-model accuracy metrics#
- **Continuous training** — incremental updates without downtime#
- **A/B testing** — compare model versions#

## Configuration#

Environment variables:
- `LEARNING_ENABLED` — enable real-time learning (default: `true`)
- `FEEDBACK_RETENTION_DAYS` — how long to keep feedback (default: `90`)
- `MODEL_UPDATE_INTERVAL` — hours between updates (default: `24`)

## API Endpoints#

- `POST /ai/feedback` — submit feedback on AI suggestion#
- `GET /ai/performance` — get model performance metrics#
- `POST /ai/adjust` — manually adjust model parameters#
- `GET /ai/ab-test-results` — get A/B test results#

## Usage#

```python
from cosmicsec_ai import HelixEngine

engine = HelixEngine()
engine.submit_feedback(threat_id, rating="helpful", notes="Good detection")
```
