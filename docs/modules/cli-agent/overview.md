# CLI Agent (cosmicsec-cli)

**Natural Language Security Operations Terminal.**

The CosmicSec CLI is a powerful terminal-based agent that allows security professionals to interact with the entire ecosystem using natural language. It combines a deterministic execution engine with an AI planner to bridge the gap between human intent and machine execution.

## 🚀 Key Features

*   **Natural Language Interaction**: Perform complex security tasks by typing instructions like "Run a full scan on example.com and report all high-risk vulnerabilities."
*   **Hybrid Execution Engine**: Combines an LLM-based planner for complex logic with a high-speed deterministic executor for standard tools.
*   **17+ Tool Parsers**: Native support for parsing output from industry-standard tools including Nmap, Nuclei, Burp Suite, ffuf, and more.
*   **Autonomous Mode**: Can operate as a standalone agent on a local machine or interact with a remote CosmicSec cluster.
*   **Rust Acceleration**: Critical parsing and data processing tasks are accelerated using internal Rust modules.

## 🏗️ Internal Architecture

Located in `src/cosmicsec_agent/`:

*   **`ai_planner.py`**: The "brain" that translates natural language into a sequence of tool invocations.
*   **`hybrid_engine.py`**: Manages the coordination between AI planning and deterministic execution.
*   **`tool_registry.py`**: Central registry of all supported security tools and their capabilities.
*   **`parsers/`**: Specialized modules that normalize heterogeneous tool outputs into a standard format.
*   **`executor.py`**: Handles the actual execution of CLI commands and capturing of output.

## 🛠️ Supported Tools

| Tool | Capability |
| :--- | :--- |
| **Nmap** | Port scanning and service discovery |
| **Nuclei** | Template-based vulnerability scanning |
| **ffuf** | Fast web fuzzing and directory discovery |
| **Burp Suite** | Web application security testing |
| **Metasploit** | Penetration testing and exploitation |
| **Sqlmap** | Automated SQL injection detection |

## 🔄 Workflow

1.  **Intent Parsing**: User enters a command (NL or traditional).
2.  **Planning**: The `ai_planner` determines the necessary steps (e.g., "First run Nmap, then Nuclei based on open ports").
3.  **Execution**: The `executor` runs the tools sequentially or in parallel.
4.  **Normalization**: Tool outputs are passed through `parsers/` to create structured data.
5.  **Aggregation**: Findings are aggregated, de-duplicated, and displayed to the user.
6.  **Reporting**: Results are optionally pushed to the central CosmicSec SOC service.

## 🛠️ Usage Example

```bash
# Natural Language Scan
cosmicsec analyze "Find open web ports on 192.168.1.1/24 and check for common misconfigurations"

# Interactive Mode
cosmicsec --interactive

# Direct Tool Invocation with Parsed Output
cosmicsec run nmap -sV -A 10.0.0.5 --output-json
```

## 📂 File Structure

```text
cosmicsec-cli/
├── src/cosmicsec_agent/
│   ├── parsers/         # Tool-specific output parsers
│   ├── ai_planner.py    # NL intent processing
│   ├── hybrid_engine.py # Coordination logic
│   ├── tool_registry.py # Integrated tools
│   └── main.py          # CLI entry point
├── packages/            # Distribution configs (Homebrew, NPM)
└── ...
```
