# Data Residency

**Data Residency Module** — Manage data locality and GDPR compliance.

## Overview

The data residency module ensures that sensitive data stays within required geographic boundaries, supporting GDPR, CCPA, and other regional regulations.

## Features

- Geographic data isolation
- Region-specific encryption keys
- Audit logging for data access
- Automatic data purging based on retention policies
- Compliance reporting dashboard

## Configuration

Environment variables:
- `DATA_RESIDENCY_REGION` — primary region (default: `us-east-1`)
- `DATA_RETENTION_DAYS` — retention period (default: `365`)
- `GDPR_ENABLED` — enable GDPR compliance (default: `true`)

## API Endpoints

- `GET /data-residency/regions` — list available regions
- `POST /data-residency/set-region` — set user's data region
- `GET /data-residency/compliance-report` — generate compliance report

## Usage

```python
from cosmicsec_core.data_residency import DataResidencyManager

manager = DataResidencyManager()
region = manager.get_user_region(user_id)
```
