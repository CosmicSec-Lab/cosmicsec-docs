# Developer Onboarding Guide

Welcome to the CosmicSec engineering team! This guide will help you set up your development environment and understand our workflows for contributing to the 9-module ecosystem.

## 🛠️ Local Environment Setup

### 1. Prerequisite Software
Ensure you have the following installed:
*   **Python 3.11+** (Primary language)
*   **Node.js 20+** (Frontend and TS SDK)
*   **Rust 1.75+** (Ingest engine and CLI acceleration)
*   **Docker & Docker Compose**
*   **Git**

### 2. Repository Management
We use a multi-repo structure. It is recommended to keep all repositories in a single root directory.

```bash
mkdir cosmicsec-lab && cd cosmicsec-lab
# Clone all repositories (Internal tool available for bulk cloning)
cosmicsec-cli internal clone-all
```

### 3. Virtual Environments
Each Python-based repository uses its own virtual environment. We recommend using `uv` or `poetry` for dependency management.

```bash
cd cosmicsec-services
uv venv
source .venv/bin/activate
uv sync
```

## 🏗️ Technical Standards

### Code Style
*   **Python**: We follow **PEP 8** and use `ruff` for linting and formatting.
*   **TypeScript**: We follow the **Airbnb Style Guide** and use `eslint` + `prettier`.
*   **Commit Messages**: We use **Conventional Commits** (e.g., `feat(api): add new scan endpoint`).

### Testing
No pull request is accepted without corresponding tests.
*   **Backend**: `pytest` for unit and integration tests.
*   **Frontend**: `vitest` for components and `playwright` for E2E.
*   **SDK**: Integration tests must pass in both Python and TypeScript.

## 🔄 Development Workflow

1.  **Issue Selection**: Pick an issue from the GitHub project board.
2.  **Branching**: Create a feature branch from `main` (e.g., `feature/issue-number-description`).
3.  **Local Development**: Run the platform locally using `docker-compose` from `cosmicsec-deploy`, then stop the service you are working on and run it locally.
4.  **Verification**: Run the full test suite and linting.
5.  **Pull Request**: Open a PR against `main`. Ensure all CI checks pass.
6.  **Code Review**: At least one senior maintainer must approve the PR.

## 🔗 Internal Resources

*   **Architecture Decision Records (ADRs)**: Found in `cosmicsec-docs/docs/architecture/adr/`.
*   **API Design Guidelines**: Found in `cosmicsec-docs/docs/api-reference/design.md`.
*   **Security Policy**: Please read the `SECURITY.md` in each repo before starting work.

## 🆘 Getting Help

*   **Slack/Discord**: Join the `#eng-general` and `#dev-support` channels.
*   **Office Hours**: Engineering syncs happen every Tuesday and Thursday at 10 AM UTC.
*   **Wiki**: Additional internal notes can be found in the private `deepintel` repository.

---

**Next Steps**: Explore the [Architecture Overview](../architecture/overview.md) to understand how the platform pieces fit together.
