# Automated Remediation#

**Automated Remediation Module** — one-click vulnerability fixes.

## Overview#

Generates and applies fix playbooks for common vulnerability types automatically.

## Features#

- **Fix playbooks** — step-by-step remediation guidance#
- **Priority-based actions** — critical issues first#
- **Integration with orchestration** — works with SOAR Engine#
- **Rollback capability** — undo changes if needed#
- **Compliance tracking** — remediation audit trail#

## Supported Vulnerability Types#

- Missing security headers#
- Outdated dependencies with CVEs#
- Weak cipher suites#
- Open ports and services#
- Misconfigured cloud resources#

## Configuration#

Environment variables:
- `REMEDIATION_ENABLED` — enable auto-remediation (default: `true`)
- `AUTO_APPLY` — automatically apply fixes (default: `false`)
- `APPROVAL_REQUIRED` — require human approval (default: `true`)

## API Endpoints#

- `POST /ai/remediate` — generate fix playbook#
- `POST /ai/apply-fix` — apply remediation#
- `GET /ai/remediation-status` — check fix status#
- `GET /ai/playbooks` — list available playbooks#
