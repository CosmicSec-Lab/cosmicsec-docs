# Threat Intel Feeds Service

**Threat Intel Feeds** — integrates with 7+ intelligence feeds for real-time threat data.

## Overview

Aggregates threat intelligence from multiple sources including commercial, open-source, and dark web feeds.

## Supported Feeds

- NIST NVD
- CISA Alerts
- AlienVault OTX
- ThreatConnect
- And more...

## Endpoints

- `GET /feeds` — list available feeds
- `GET /feed/{name}` — get latest indicators

## Configuration

Environment variables:
- `FEED_REFRESH_INTERVAL` — refresh interval in seconds
