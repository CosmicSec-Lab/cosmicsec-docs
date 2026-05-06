# Getting Started

Welcome to CosmicSec! This guide will help you get up and running with the platform quickly.

## Prerequisites

- **Docker** & Docker Compose (recommended)
- **Python 3.8+** (for CLI/SDK)
- **Node.js 18+** (for web dashboard)
- **Git**

## Installation Options

### Option 1: Docker Compose (Recommended)

```bash
# Clone the repository
git clone https://github.com/cosmicsec/cosmicsec-services.git
cd cosmicsec-services

# Start all services
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f
```

### Option 2: Python SDK

```bash
# Install the SDK
pip install cosmicsec-sdk[full]

# Verify installation
python -c "import cosmicsec_sdk; print('Success!')"

# Quick test
cosmicsec health
```

### Option 3: TypeScript SDK

```bash
# Install the TypeScript SDK
npm install @cosmicsec/sdk-ts

# Use in your project
import { CosmicSecClient } from '@cosmicsec/sdk-ts';
```

## Quick Start

### 1. Configure Authentication

```bash
# Set your API key
export COSMICSEC_API_KEY="your-api-key-here"

# Or use the CLI
cosmicsec auth login
```

### 2. Run Your First Scan

```bash
# Using CLI
cosmicsec scan create --target example.com --type nmap

# Using Python SDK
from cosmicsec_sdk import CosmicSecClient

client = CosmicSecClient()
scan = client.create_scan({"target": "example.com", "scan_types": ["nmap"]})
print(f"Scan created: {scan['scan_id']}")
```

### 3. Access the Web Dashboard

Open your browser and navigate to:
- **Local**: http://localhost:3000
- **Default credentials**: admin / admin (change immediately!)

## Configuration

### Environment Variables

```bash
# Core settings
COSMICSEC_API_KEY=your-api-key
COSMICSEC_BASE_URL=https://api.cosmicsec.com
COSMICSEC_ENVIRONMENT=production

# Advanced settings
COSMICSEC_ENABLE_CACHE=true
COSMICSEC_MAX_RETRIES=3
COSMICSEC_TIMEOUT=30
COSMICSEC_LOG_LEVEL=INFO
```

### Configuration File

Create `~/.cosmicsec/config.json`:

```json
{
  "api_key": "your-api-key",
  "base_url": "https://api.cosmicsec.com",
  "environment": "production",
  "enable_cache": true,
  "max_retries": 3,
  "profiles": {
    "staging": {
      "base_url": "https://staging.api.cosmicsec.com"
    }
  }
}
```

## Next Steps

- [Run your first security scan](/getting-started/first-scan)
- [Explore the CLI commands](/cli/commands)
- [Set up notifications](/integrations/communication)
- [Configure compliance reporting](/enterprise/compliance)

## Troubleshooting

### Connection Issues

```bash
# Test connectivity
cosmicsec health

# Check logs
docker-compose logs -f api-gateway
```

### Authentication Errors

```bash
# Verify API key
cosmicsec auth verify

# Reset credentials
cosmicsec auth login --reset
```

Need help? Join our [Discord community](https://discord.gg/cosmicsec) or check the [FAQ](/troubleshooting/faq).
