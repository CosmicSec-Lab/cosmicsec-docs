# Contributing Guide#

**How to Contribute to CosmicSec.**

## Overview#

Learn how to contribute code, documentation, and fixes to CosmicSec.

## Getting Started#

1. **Fork the repository** on GitHub#
2. **Clone your fork**: `git clone https://github.com/your-username/cosmicsec.git`#
3. **Create branch**: `git checkout -b feature/my-feature`#
4. **Make changes** and commit#
5. **Push**: `git push origin feature/my-feature`#
6. **Create Pull Request**#

## Code Standards#

- **Python**: PEP 8, type hints, docstrings#
- **TypeScript**: ESLint, Prettier#
- **Commits**: Conventional Commits format#
- **Tests**: Add tests for new features#

## Development Setup#

```bash#
# Python services#
cd cosmicsec-services#
pip install -r requirements/dev.txt#
pytest#

# Frontend#
cd cosmicsec-web/frontend#
npm install#
npm test#
```

## Pull Request Checklist#

- [ ] Tests added/updated#
- [ ] Documentation updated#
- [ ] Lint passes#
- [ ] Type hints added#
- [ ] Docstrings added#
