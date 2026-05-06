# Session#

**Session Messenger Integration** — encrypted messaging.

## Overview#

Access and analyze content on the Session encrypted messaging platform.

## Features#

- **Session crawling** — access Session content#
- **Encrypted messaging** — Session's encryption#
- **Group monitoring** — monitor Session groups#
- **Anonymous IDs** — Session's anonymous identities#

## Configuration#

Environment variables:
- `SESSION_ENABLED` — enable Session integration (default: `true`)
- `SESSION_HOST` — Session host (default: `127.0.0.1`)
- `SESSION_PORT` — Session port (default: `5942`)

## API Endpoints#

- `POST /deepintel/session/send` — send Session message#
- `GET /deepintel/session/receive` — receive messages#
- `POST /deepintel/session/monitor` — monitor Sessions#

## Usage#

```python
from cosmicsec_deepintel import SessionClient#

client = SessionClient()
messages = client.receive()
```
