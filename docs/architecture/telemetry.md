# Telemetry & Observability

CosmicSec implements a modern, cloud-native observability stack based on **OpenTelemetry**, **Structured Logging**, and **Distributed Tracing**. This allows operators and developers to gain deep insights into the platform's performance, behavior, and security events.

## 📊 The Observability Stack

*   **Structured Logging**: All services use `structlog` to emit logs in JSON format. This makes them easily searchable and indexable by tools like Elasticsearch, Loki, or Datadog.
*   **Distributed Tracing**: Powered by OpenTelemetry (OTel). Requests are traced as they flow through the system, from the API Gateway to the various microservices and the AI Helix engine.
*   **Metrics**: Services export Prometheus-compatible metrics for real-time monitoring of latency, throughput, and error rates.
*   **Error Tracking**: Deep integration with Sentry for automated exception reporting and profiling.

## 🕷️ Distributed Tracing with OpenTelemetry

Tracing is enabled by default in production-ready deployments. It uses the OTLP (OpenTelemetry Line Protocol) to export spans to a collector (e.g., Jaeger, Tempo, or Honeycomb).

### Configuration

The following environment variables control the telemetry behavior:

| Variable | Description | Default |
| :--- | :--- | :--- |
| `OTEL_ENABLED` | Enable/Disable OTel instrumentation. | `false` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | The OTLP collector endpoint. | `http://jaeger:4317` |
| `OTEL_SERVICE_NAME` | The name of the service for tracing. | (service-specific) |
| `APP_ENV` | The deployment environment (dev, prod). | `production` |

### Visualizing Traces

In the standard `cosmicsec-deploy` stack, you can access the Jaeger UI to view traces:

1.  Open `http://localhost:16686` in your browser.
2.  Select a service (e.g., `api-gateway`) and click **Find Traces**.
3.  Click on a trace to see the full request lifecycle across multiple services.

## 📝 Structured Logging

Logs are emitted in JSON format, including critical context like `request_id`, `user_id`, and `duration_ms`.

### Example Log Entry

```json
{
  "event": "request_finished",
  "level": "info",
  "method": "POST",
  "path": "/api/v1/scans",
  "request_id": "1715500000000",
  "status_code": 200,
  "duration_ms": 145.2,
  "timestamp": "2024-05-12T10:00:00.123456Z"
}
```

## 📉 Metrics & Dashboards

CosmicSec provides several pre-configured Grafana dashboards to monitor the platform:

*   **System Health**: CPU, Memory, and Disk usage across all nodes.
*   **API Performance**: Latency (P95/P99), Error Rates, and Request Volume per endpoint.
*   **AI Helix Analytics**: LLM token usage, agent execution duration, and RAG accuracy.
*   **SOC Operations**: Active threats, scan throughput, and remediation success rates.

Dashboards are available at `http://localhost:3001` (default credentials: `admin`/`admin`).

## 🛠️ Developer Guide: Adding Instrumentation

To add custom spans or logs in your service:

```python
import structlog
from opentelemetry import trace

logger = structlog.get_logger()
tracer = trace.get_tracer(__name__)

async def process_scan(scan_id: str):
    with tracer.start_as_current_span("process_scan_logic") as span:
        span.set_attribute("scan.id", scan_id)
        
        logger.info("starting_scan_processing", scan_id=scan_id)
        # ... logic ...
        logger.info("scan_processing_complete", scan_id=scan_id)
```
