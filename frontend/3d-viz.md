# 3D Visualization Page#

**Three.js Integration** — interactive 3D network graphs.

## Overview#

Visualize network topology and threats in 3D using Three.js.

## Features#

- **Interactive 3D** — rotate, zoom, pan network graphs#
- **Node types** — servers, endpoints, threats, attackers#
- **Real-time updates** — WebSocket live data#
- **Threat indicators** — color-coded by severity#
- **Drill-down** — click nodes for details#

## Usage#

```typescript#
import { ThreeJSVisualization } from '@/components';#

<ThreeJSVisualization#
  showThreats={true}#
  refreshInterval={5000}#
/>#
```

## Visualizations#

- **Network topology** — map of all assets#
- **Attack paths** — visualize attack chains#
- **Threat landscape** — 3D threat distribution#
- **Traffic flow** — animated network traffic#

## Customization#

- **Node colors** — by type/severity#
- **Edge styles** — by traffic type#
- **Camera angles** — preset views#
- **Animations** — enable/disable#
