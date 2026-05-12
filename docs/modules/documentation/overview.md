# Documentation Platform (cosmicsec-docs)

**The Knowledge Base of the CosmicSec Ecosystem.**

`cosmicsec-docs` is the central repository for all technical documentation, architectural diagrams, and user guides for the CosmicSec platform. It follows a "Documentation-as-Code" philosophy, ensuring that our documentation is version-controlled, modular, and high-quality.

## 🚀 Key Features

*   **Modular Architecture**: Documentation is organized into logical sections covering Architecture, Modules, Guides, and API References.
*   **Markdown-First**: All content is written in GitHub-flavored Markdown for maximum portability and ease of contribution.
*   **Mermaid.js Integration**: Built-in support for diagrams-as-code, including sequence diagrams, flowcharts, and dependency maps.
*   **Static Site Generation**: (Planned) Integrated with MkDocs or Docusaurus for a modern, searchable web-based documentation portal.
*   **Cross-Module Linking**: Intelligent cross-references ensure that users can easily navigate between related technical concepts.

## 🏗️ Internal Structure

The documentation is organized as follows:

*   **`docs/architecture/`**: High-level system design, security models, and data flows.
*   **`docs/modules/`**: Deep-dive technical guides for each of the 9 platform modules.
*   **`docs/guides/`**: Actionable guides for installation, quick-start, and developer onboarding.
*   **`docs/api-reference/`**: Detailed references for REST, GraphQL, and WebSocket APIs.
*   **`docs/community/`**: Information on contributing, roadmap, and code of conduct.
*   **`docs/resources/`**: Glossary, FAQ, and troubleshooting information.

## 🔄 Documentation Workflow

1.  **Code Changes**: When a developer modifies code in any module, they are responsible for identifying the impact on documentation.
2.  **Doc Update**: The developer opens a PR in `cosmicsec-docs` with the updated Markdown files.
3.  **Review**: Documentation changes are reviewed for technical accuracy and clarity by senior maintainers.
4.  **Publish**: Approved changes are merged into the `main` branch and automatically updated on the documentation portal.

## 🛡️ Special Handling for DeepIntel

To maintain operational security, detailed internal documentation for the `cosmicsec-deepintel` module is kept in its own private repository. `cosmicsec-docs` provides a high-level overview and points authorized users toward the private resources.

## 🛠️ Contribution Guidelines

We welcome documentation contributions! Please see our [Contribution Guidelines](../../community/contribution.md) for more information.
