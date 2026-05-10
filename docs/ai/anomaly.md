# Anomaly Detection#

**Anomaly Detection Module** — identify unusual patterns.

## Overview#

Uses statistical and ML methods to detect anomalies in security data.

## Methods#

- **Isolation Forest** — tree-based anomaly detection#
- **Z-score** — statistical outlier detection#
- **Moving Average** — trend deviation detection#
- **Seasonal decomposition** — detect seasonal anomalies#

## Features#

- **Real-time detection** — analyze streaming data#
- **Baseline learning** — learn normal behavior patterns#
- **Severity scoring** — prioritize anomalies#
- **Alert generation** — automatic alerts on detection#

## Configuration#

Environment variables:
- `ANOMALY_ENABLED` — enable anomaly detection (default: `true`)
- `METHOD` — detection method (default: `isolation_forest`)
- `Z_SCORE_THRESHOLD` — z-score threshold (default: `3.0`)
- `CONTAMINATION` — expected outlier fraction (default: `0.1`)

## API Endpoints#

- `POST /ai/detect-anomaly` — detect anomalies in data#
- `GET /ai/anomaly-baseline` — view learned baseline#
- `POST /ai/anomaly/retrain` — retrain detection model#
- `GET /ai/anomalies` — list detected anomalies#

## Usage#

```python
from cosmicsec_ai import AnomalyDetector

detector = AnomalyDetector()
anomalies = detector.detect(network_traffic_data)
```
