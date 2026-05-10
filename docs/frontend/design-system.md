# Design System#

**Glassmorphism Design System** — premium UI components.

## Overview#

Consistent design system with glassmorphism aesthetics.

## Design Tokens#

### Colors#

- **Primary**: `#6366f1` (indigo)#
- **Secondary**: `#8b5cf6` (violet)#
- **Accent**: `#06b6d4` (cyan)#
- **Background**: `#0f172a` (slate-900)#

### Blur Effects#

- **Light**: `backdrop-blur(10px)`#
- **Medium**: `backdrop-blur(15px)`#
- **Heavy**: `backdrop-blur(20px)`#

## Components (20+)#

- **SecurityDashboard** — main operations view#
- **ThreatHuntingDashboard** — proactive hunting#
- **ThreeJSVisualization** — 3D network graphs#
- **IoTDashboard** — IoT/OT monitoring#
- **SLAManager** — SLA tracking#
- **ThemeBuilder** — customize glassmorphism#
- **NLPSearch** — natural language search#
- **CommandPalette** — keyboard shortcuts#
- **NotificationBell** — real-time alerts#

## Theme Presets#

- **Cosmic Dark** — default dark theme#
- **Midnight Blue** — blue-tinted dark#
- **Neon Nights** — vibrant neon accents#
- **Emerald Forest** — green-tinted dark#

## Usage#

```typescript#
import { SecurityDashboard } from '@/components';#

<SecurityDashboard
  showThreats={true}
  refreshInterval={30000}
/>#
