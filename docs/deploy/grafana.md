# Grafana Dashboards#

**5+ Monitoring Dashboards** — service health, AI performance, business metrics.

## Overview#

Monitor CosmicSec with pre-built Grafana dashboards.

## Available Dashboards#

- **Service Health** — uptime, memory, CPU per service#
- **AI Performance** — inference latency, accuracy, queue depth#
- **Business Metrics** — scans per day, incidents resolved#
- **Scan Pipeline** — scan progress, success/failure rates#
- **CosmicSec Overview** — high-level system health#

## Accessing Grafana#

```bash#
# Port-forward to Grafana#
kubectl port-forward svc/grafana 3000:3000 -n cosmicsec#

# Open in browser#
http://localhost:3000#
```

Default credentials:
- **Username**: `admin`#
- **Password**: `cosmicsec-admin` (change in production!)#

## Importing Dashboards#

```bash#
# Import dashboards#
kubectl apply -f cosmicsec-deploy/k8s/monitoring/dashboards/#
```

Dashboard JSON files location:
- `cosmicsec-deploy/infrastructure/grafana/dashboards/`#

## Creating Custom Dashboards#

```bash#
# Export existing dashboard#
curl -H "Authorization: Bearer $API_KEY" \
  http://grafana:3000/api/dashboards/uid/cosmicsec-ai-performance \
  > my-dashboard.json#
```

## Alerting#

- **Service down** — alert when service is unavailable#
- **High latency** — alert when p99 > 500ms#
- **AI errors** — alert when error rate > 5%#
- **Queue depth** — alert when queue > 1000 items#

## Metrics Collected#

- **Prometheus** — metrics endpoint on each service#
- **Grafana** — visualization and alerting#
- **Alertmanager** — route alerts to Slack/email#
