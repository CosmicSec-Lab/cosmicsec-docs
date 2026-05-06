# Docker Compose#

**Local Development** — quick setup with Docker Compose.

## Overview#

Deploy CosmicSec locally using Docker Compose for development and testing.

## Prerequisites#

- **Docker** 20.10+#
- **Docker Compose** 2.0+#

## Quick Start#

```bash#
# Clone repository#
git clone https://github.com/cosmicsec/cosmicsec.git#
cd cosmicsec#

# Start all services#
docker-compose up -d#

# Check status#
docker-compose ps#
```

## Configuration#

```bash#
# Copy example env file#
cp .env.example .env#

# Edit configuration#
nano .env#
```

Environment variables:
- `POSTGRES_PASSWORD` — PostgreSQL password (required)#
- `REDIS_PASSWORD` — Redis password (required)#
- `MONGO_PASSWORD` — MongoDB password (required)#
- `JWT_SECRET_KEY` — JWT signing key (use Vault in production)#

## Services#

- **Frontend**: http://localhost:3000#
- **API Gateway**: http://localhost:8000#
- **API Docs**: http://localhost:8000/docs#
- **GraphQL**: http://localhost:8000/graphql#
- **Traefik Dashboard**: http://localhost:8080#

## Common Commands#

```bash#
# View logs#
docker-compose logs -f [service_name]#

# Restart service#
docker-compose restart [service_name]#

# Stop all#
docker-compose down#

# Clean restart#
docker-compose down -v  # Removes volumes#
docker-compose up -d#
```

## Troubleshooting#

```bash#
# Check resource usage#
docker stats#

# Access container shell#
docker-compose exec api_gateway bash#

# Check network#
docker network inspect cosmicsec-network#
```
