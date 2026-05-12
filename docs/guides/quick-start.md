# Quick Start Guide

Deploy the entire CosmicSec ecosystem in minutes using Docker Compose.

## 📋 Prerequisites

Before you begin, ensure you have the following installed on your machine:

*   **Docker**: Version 24.0.0 or higher.
*   **Docker Compose**: Version 2.20.0 or higher.
*   **Git**: To clone the repositories.
*   **Python 3.11+**: Optional, for local CLI and SDK usage.

## 🚀 Deployment Steps

### 1. Clone the Deployment Repository
The `cosmicsec-deploy` repository contains the master orchestration files.

```bash
git clone https://github.com/cosmicsec/cosmicsec-deploy.git
cd cosmicsec-deploy
```

### 2. Initialize Environment
Run the setup script to generate necessary environment variables and configuration files.

```bash
./scripts/setup_env.sh
```

### 3. Start the Platform
Pull the latest images and start all services in detached mode.

```bash
docker-compose up -d
```

### 4. Verify Installation
Check the status of the containers to ensure everything is running correctly.

```bash
docker-compose ps
```

## 🌐 Accessing the Platform

Once the deployment is complete, you can access the various platform components:

| Component | URL | Credentials (Default) |
| :--- | :--- | :--- |
| **Web Frontend** | `http://localhost:3000` | `admin@cosmicsec.local` / `CosmicSec2024!` |
| **API Gateway (Docs)** | `http://localhost:8000/docs` | N/A |
| **SOC Dashboard** | `http://localhost:3000/soc` | N/A |
| **Grafana** | `http://localhost:3001` | `admin` / `admin` |

## 🛠️ Your First Scan

After logging in to the Web Frontend:

1.  Navigate to the **Scanner** tab.
2.  Click **New Scan**.
3.  Enter a target (e.g., `https://test-target.com`).
4.  Select a profile (e.g., **Standard Web Scan**).
5.  Click **Launch**.

You can monitor the scan's progress in real-time on the **Live Scan** dashboard.

## 🛑 Stopping the Platform

To stop and remove all containers:

```bash
docker-compose down
```

---

**Next Steps**: Check out the [Installation Guide](./installation.md) for more advanced deployment options, including Kubernetes and Cloud-native setups.
