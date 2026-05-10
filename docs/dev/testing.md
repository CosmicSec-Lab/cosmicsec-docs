# Testing Guide#

**Testing CosmicSec Components.**

## Overview#

Learn how to test CosmicSec services, SDKs, and frontend.

## Running Tests#

### Python Services#

```bash#
cd cosmicsec-services#
pytest#
pytest --cov=services#
pytest tests/unit/test_api_router.py#
```

### Frontend#

```bash#
cd cosmicsec-web/frontend#
npm test#
npm run test:coverage#
npm run test:e2e#
```

### SDKs#

```bash#
# Python SDK#
cd cosmicsec-sdk#
pytest tests/#

# TypeScript SDK#
cd cosmicsec-sdk/sdk/typescript#
npm test#
```

## Test Types#

- **Unit tests** — test individual functions#
- **Integration tests** — test service interactions#
- **E2E tests** — test full workflows#
- **Performance tests** — benchmark critical paths#

## Coverage Goals#

- **Python**: 85%+ coverage#
- **TypeScript**: 80%+ coverage#
- **Critical paths**: 100% coverage#
