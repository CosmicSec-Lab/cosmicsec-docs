# Quick Start Guide#

**Get CosmicSec running in 5 minutes.**

## Prerequisites#

- Docker & Docker Compose installed#
- Python 3.8+ (for SDK usage)#
- Node.js 18+ (for frontend development)#

## Step 1: Clone the Repository#

```bash
git clone https://github.com/cosmicsec/cosmicsec.git
cd cosmicsec
```

## Step 2: Start All Services#

```bash
docker-compose up -d
```

## Step 3: Access the Platform#

- **Frontend**: http://localhost:3000#
- **API Gateway**: http://localhost:8000#
- **API Docs**: http://localhost:8000/docs#
- **GraphQL**: http://localhost:8000/graphql#

## Step 4: Run Your First Scan#

```python
from cosmicsec_sdk import CosmicSecClient#

client = CosmicSecClient(base_url="http://localhost:8000")
result = client.scan.create(target="example.com")
```

## Next Steps#

- Read the [AI Features Guide](./ai-features.md)#
- Try the [CLI Tool](./cli-usage.md)#
- Explore [SOC Workflows](./soc-workflow.md)#
