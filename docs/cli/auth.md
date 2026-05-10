# CLI Authentication#

**Authentication & Sessions** — secure CLI access.

## Overview#

Manage CLI authentication with API keys, OAuth2, and SSO.

## Authentication Methods#

- **API Key** — simple token-based auth#
- **OAuth2** — authorize via browser#
- **SSO** — SAML/OIDC integration#
- **2FA** — two-factor authentication#

## Using API Keys#

```bash#
# Set API key#
cosmicsec config set api_key "your_key"#

# Or use environment variable#
export COSMICSEC_API_KEY="your_key"#
cosmicsec scan list#
```

## OAuth2 Flow#

```bash#
# Login via OAuth2#
cosmicsec auth login --method oauth2#

# Browser will open for authorization#
# After authorization, CLI is authenticated#
```

## Session Management#

```bash#
# Check session status#
cosmicsec auth status#

# Logout#
cosmicsec auth logout#

# Refresh session#
cosmicsec auth refresh#
```

## Configuration#

Environment variables:
- `COSMICSEC_API_KEY` — API key (preferred over config file)#
- `COSMICSEC_ENDPOINT` — API endpoint (default: `https://api.cosmicsec.com`)#
- `COSMICSEC_TIMEOUT` — request timeout (default: `30`)#

## Security#

- **API keys stored** in `~/.cosmicsec/config.json` (permissions: `600`)#
- **Sessions** are JWT tokens with expiration#
- **2FA** uses TOTP (Google/Microsoft Authenticator)#
