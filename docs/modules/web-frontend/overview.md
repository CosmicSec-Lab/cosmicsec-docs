# Web Frontend (cosmicsec-web)

**The Command Center for CosmicSec Analysts.**

The CosmicSec Web Frontend is a high-performance, real-time command center designed for security analysts and SOC teams. It provides a polished, modern interface for managing scans, investigating threats, and collaborating with team members.

## 🚀 Key Features

*   **React 19 & Next.js**: Built on the latest web technologies for speed and SEO.
*   **Glassmorphism Design**: A premium, modern UI with interactive transparency and depth.
*   **Real-time Dashboards**: Live SOC updates and scan progress via WebSockets.
*   **3D Attack Path Visualization**: Powered by Three.js to visualize complex network attack chains.
*   **Mobile-First Design**: Fully responsive web app with a dedicated React Native mobile version.
*   **PWA Support**: Installable as a progressive web app for native-like performance on desktop and mobile.

## 🏗️ Internal Architecture

Located in `apps/web/src/`:

*   **`components/`**: Modular UI components, including specialized dashboards (IoT, Security, Threat Hunting).
*   **`context/` / `store/`**: Global state management using React Context and Zustand.
*   **`hooks/`**: Custom hooks for API interaction, WebSocket handling, and theme management.
*   **`services/`**: Integration layer for communicating with the CosmicSec API Gateway and SDK.
*   **`lib/`**: Core utilities, including the Three.js visualization engine.

## 📊 Specialized Dashboards

### 1. Security Dashboard
A high-level overview of organization security posture, including active scans, open vulnerabilities, and risk scores.

### 2. Threat Hunting Dashboard
Deep integration with DeepIntel and AI Helix for investigating emerging threats and darknet activities.

### 3. IoT/OT Dashboard
Specialized view for monitoring industrial and internet-of-things device security.

### 4. 3D Attack Path
An interactive 3D visualization that maps how an attacker could move through the network, identifying critical paths and choke points.

## 🔄 Technical Stack

| Category | Technology |
| :--- | :--- |
| **Framework** | React 19, Next.js |
| **State Management** | Zustand |
| **Styling** | Vanilla CSS, TailwindCSS (for rapid prototyping) |
| **Visualization** | Three.js, D3.js |
| **Real-time** | Socket.io / Native WebSockets |
| **Validation** | Zod |

## 🛠️ Developer Workflow

1.  **Component Creation**: Build UI elements in `src/components/ui/`.
2.  **API Integration**: Define API calls in `src/services/` using the TypeScript SDK.
3.  **State Management**: Update the Zustand store to handle new data flows.
4.  **Testing**: Run unit tests with Vitest and E2E tests with Playwright.

## 📂 File Structure

```text
cosmicsec-web/
├── apps/
│   ├── web/            # Next.js Web Application
│   │   ├── src/
│   │   └── public/
│   └── mobile/         # React Native Mobile Application
│       └── src/
├── assets/             # Brand and UI assets
└── ...
```
