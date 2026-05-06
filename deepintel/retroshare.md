# RetroShare#

**RetroShare Integration** — friend-to-friend sharing.

## Overview#

Access and analyze content on the RetroShare friend-to-friend network.

## Features#

- **RetroShare crawling** — access RetroShare locations#
- **Friend-to-friend** — F2F network access#
- **Content discovery** — find RetroShare resources#
- **Anonymous messaging** — RetroShare's secure messaging#

## Configuration#

Environment variables:
- `RETROSHARE_ENABLED` — enable RetroShare integration (default: `true`)
- `RETROSHARE_HOST` — RetroShare host (default: `127.0.0.1`)
- `RETROSHARE_PORT` — RetroShare port (default: `4012`)

## API Endpoints#

- `POST /deepintel/retroshare/crawl` — crawl RetroShare location#
- `GET /deepintel/retroshare/search` — search RetroShare#
- `POST /deepintel/retroshare/monitor` — monitor RetroShare location#

## Usage#

```python
from cosmicsec_deepintel import RetroShareClient

client = RetroShareClient()
content = client.crawl("retroshare://location-id")
```
