# FAQ & Troubleshooting

Find answers to common questions and solutions for common issues encountered while using the CosmicSec platform.

## ❓ General Questions

### What is CosmicSec?
CosmicSec is an enterprise-grade cybersecurity operations ecosystem that leverages AI and a modular microservices architecture to automate threat detection, analysis, and remediation.

### Which LLMs does AI Helix support?
AI Helix supports a variety of models including OpenAI (GPT-4o), Anthropic (Claude 3.5 Sonnet), and local models via Ollama (Llama 3.1, Mistral).

### Is CosmicSec open source?
The Core Platform and many of its modules are open source. Some advanced modules, such as DeepIntel PRO, are proprietary and available only to enterprise customers.

## 🛠️ Troubleshooting

### Why is my scan not starting?
1.  **Check Service Health**: Ensure the `scan_service` and `core_gateway` are running.
2.  **Resource Limits**: Verify that your cluster has enough CPU/RAM to launch new scan containers.
3.  **Authentication**: Ensure your JWT is valid and has the `scans:write` scope.

### I'm seeing "Connection Refused" when accessing the API.
1.  **Network Configuration**: Verify that your API Gateway is accessible on the configured port (default: 8000).
2.  **Firewall/VPN**: Ensure your network allows traffic to the platform's IP/domain.
3.  **Gateway Status**: Check if the `api_gateway` container has crashed or is in a restart loop.

### How do I reset my admin password?
You can use the internal CLI tool to reset passwords directly in the database:
```bash
cosmicsec-cli internal auth reset-password --email admin@cosmicsec.local --new-password MyNewSecret123!
```

## 🆘 Still Need Help?

*   **GitHub Issues**: Open an issue in the relevant repository.
*   **Documentation**: Search the full documentation for more specific guides.
*   **Support**: Enterprise customers can reach out to `support@cosmicsec.com`.
