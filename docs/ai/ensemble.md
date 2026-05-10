# Multi-Model Ensemble AI#

**Helix AI Engine** — multi-model ensemble with 30+ endpoints.

## Overview#

Combines multiple LLM providers for superior accuracy and resilience.

## Supported Models#

- **OpenAI GPT-4o** — advanced reasoning#
- **Anthropic Claude 3 Opus** — deep analysis#
- **Meta Llama 3.1 70B** — local inference#
- **Ollama** — offline capable#

## Features#

- **Automatic fallback** — switch models on failure#
- **Consensus mode** — combine model outputs#
- **Cost optimization** — route to cheapest model#
- **Latency optimization** — parallel model calls#

## API Endpoints#

- `POST /ai/ensemble-predict` — get ensemble prediction#
- `POST /ai/consensus` — get consensus from multiple models#
- `GET /ai/models` — list available models#
- `POST /ai/set-model-preference` — set preferred model#

## Usage#

```python
from cosmicsec_ai import HelixEngine#

engine = HelixEngine()
result = engine.ensemble_predict(threat_data)
```
