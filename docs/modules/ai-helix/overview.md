# AI Helix (cosmicsec-ai)

**The Autonomous Brain of the CosmicSec Ecosystem.**

AI Helix is a sophisticated autonomous agent platform designed to perform complex security analysis, threat hunting, and automated remediation. It leverages modern LLMs and graph-based agent orchestration to handle security tasks that traditionally require manual expert intervention.

## 🚀 Key Features

*   **Autonomous Security Agents**: Self-directing agents that can plan and execute multi-step security investigations.
*   **LangGraph Orchestration**: Uses graph-based state management to handle complex, non-linear workflows.
*   **Multi-Model Ensemble**: Supports OpenAI (GPT-4o), Anthropic (Claude 3.5 Sonnet), and local models via Ollama (Llama 3.1).
*   **Advanced Threat Analysis**: Includes specialized modules for Anomaly Detection, Attack Chain Analysis, and Zero-Day Prediction.
*   **Adaptive Intelligence**: Learns from environment feedback to improve detection accuracy and remediation strategies.

## 🏗️ Internal Architecture

AI Helix is organized into specialized sub-packages within `src/ai_service/`:

*   **`agents/`**: Core agent logic, including the `AutonomousPipeline` and `LangGraph` flow definitions.
*   **`analysis/`**: Mathematical and heuristic analysis modules (Anomaly, MITRE mapping, Zero-Day).
*   **`intelligence/`**: Threat intelligence generation and predictive modeling.
*   **`llm/`**: Provider-agnostic wrappers for interacting with various LLMs.
*   **`knowledge/`**: RAG (Retrieval-Augmented Generation) system and Vector Store integration.
*   **`security/`**: AI-specific security guardrails and the Security Copilot engine.

## 🔄 Core Workflow: Recon-to-Report

1.  **Recon**: The agent triggers the `recon_service` to gather initial data.
2.  **Scan**: It analyzes recon results and schedules appropriate SAST/DAST scans.
3.  **Analyze**: Findings are correlated against the MITRE ATT&CK framework and historical data.
4.  **Prioritize**: The AI assigns a risk score and prioritizes findings for the analyst.
5.  **Remediate**: If authorized, it generates and applies auto-remediation patches or configuration changes.
6.  **Report**: A detailed technical report is generated, including the full evidence chain.

## 🛡️ Security & Guardrails

*   **AI Guard**: A specialized middleware that inspects AI-generated commands and code for safety before execution.
*   **Human-in-the-loop**: Critical actions require manual approval from a SOC analyst.
*   **Deterministic Validation**: AI-suggested findings are cross-verified by traditional security tools.

## 🛠️ Usage Example

```python
from cosmicsec_sdk import CosmicSecClient

client = CosmicSecClient()

# Invoke the AI Agent for a targeted analysis
response = client.ai.analyze(
    target="https://api.example.com",
    goal="Identify potential OWASP Top 10 vulnerabilities and suggest mitigations",
    autonomous=True
)

print(f"Agent Status: {response.status}")
print(f"Findings: {response.findings_summary}")
```

## 📂 File Structure

```text
cosmicsec-ai/
├── src/ai_service/
│   ├── agents/          # Agent workflows (LangGraph, Pipeline)
│   ├── analysis/        # Detection logic (Anomaly, MITRE)
│   ├── intelligence/    # Threat intel & prediction
│   ├── knowledge/       # RAG & Vector store
│   ├── llm/             # LLM providers
│   ├── security/        # AI Guard & Copilot
│   └── main.py          # FastAPI Entry point
└── ...
```
