# Dashboard Page#

**Security Dashboard** — main operations center.

## Overview#

The main dashboard provides an overview of security status across all monitored assets.

## Features#

- **Threat summary** — current threat landscape#
- **Scan status** — active and recent scans#
- **Incident tracking** — open incidents#
- **Trend charts** — attack trends over time#
- **Asset overview** — monitored assets#
- **Recent activity** — latest security events#

## Components#

- **SecurityDashboard** — main dashboard component#
- **ThreatHuntingDashboard** — proactive hunting#
- **IoTDashboard** — IoT/OT monitoring#
- **ThreeJSVisualization** — 3D network graphs#

## Usage#

```typescript#
import { SecurityDashboard } from '@/components';#

<SecurityDashboard#
  showThreats={true}#
  refreshInterval={30000}#
/>#
```

## Customization#

- **Drag-and-drop** — rearrange widgets#
- **Custom filters** — filter by severity, asset, time#
- **Export** — PDF/PNG export of dashboard#
- **Share** — share dashboard with team#
