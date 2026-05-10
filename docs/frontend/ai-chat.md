# AI Chat Page#

**AI Chat Interface** — natural language queries.

## Overview#

Chat with Helix AI using natural language for security analysis.

## Features#

- **Natural language** — ask questions in plain English#
- **Context awareness** — understands security context#
- **Playbook generation** — get step-by-step guidance#
- **Code analysis** — paste code for security review#
- **Visualization** — charts and graphs in chat#

## Usage#

```typescript#
import { AIChatPage } from '@/pages';#

<AIChatPage#
  model="gpt-4o"#
  showSuggestions={true}#
/>#
```

## Example Queries#

- "Analyze this threat: ..."#
- "How do I fix a SQL injection?"#
- "Show me recent attacks on port 443"#
- "Generate a remediation playbook for XSS"#
- "What's the MITRE ATT&CK mapping for phishing?"#

## Features#

- **Streaming responses** — see AI response as it's generated#
- **History** — revisit past conversations#
- **Export** — save chat as PDF/Markdown#
- **Sharing** — share AI insights with team#
