# STIX Export#

**STIX 2.1 Export** — industry standard format.

## Overview#

Export threat intelligence in STIX 2.1 format for interoperabilty.

## Features#

- **STIX 2.1 compliance** — full standard support#
- **Bundle generation** — create STIX bundles#
- **Indicator export** — export IOCs as STIX indicators#
- **TTP mapping** — map to STIX TTPs#
- **Import support** — import STIX from other tools#

## Configuration#

Environment variables:
- `STIX_ENABLED` — enable STIX export (default: `true`)
- `STIX_VERSION` — STIX version (default: `2.1`)
- `AUTO_EXPORT` — automatically export new intel (default: `false`)

## API Endpoints#

- `POST /deepintel/stix/export` — export intel as STIX#
- `POST /deepintel/stix/import` — import STIX bundle#
- `GET /deepintel/stix/bundles` — list exported bundles#
- `GET /deepintel/stix/indicators` — list STIX indicators#

## Usage#

```python
from cosmicsec_deepintel import STIXExporter#

exporter = STIXExporter()
bundle = exporter.export(threat_intel)
```
