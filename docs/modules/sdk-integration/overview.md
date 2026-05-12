# SDK Integration (cosmicsec-sdk)

**Programmatic Access to the CosmicSec Ecosystem.**

The CosmicSec SDK provides developers with high-level, language-idiomatic libraries for interacting with the platform's APIs, services, and AI engine. It simplifies authentication, data validation, and asynchronous communication.

## 🚀 Key Features

*   **Multi-Language Support**: Official SDKs for Python and TypeScript.
*   **Type Safety**: Full typing support via Pydantic (Python) and Zod (TypeScript).
*   **Async-First**: Built with `asyncio` and `Promise`-based patterns for high-concurrency security tasks.
*   **Unified Client**: A single `CosmicSecClient` provides access to all ecosystem modules (AI, Scan, DeepIntel, etc.).
*   **Built-in Auth**: Automatic JWT management and token refreshing.

## 🐍 Python SDK

The Python SDK is the primary tool for security researchers and internal automation.

*   **Installation**: `pip install cosmicsec-sdk`
*   **Usage**:
    ```python
    from cosmicsec_sdk import CosmicSecClient
    import asyncio

    async def main():
        async with CosmicSecClient() as client:
            # Trigger a scan
            scan = await client.scans.create(target="https://target.com")
            print(f"Scan ID: {scan.id}")
            
            # Query AI Insights
            report = await client.ai.get_report(scan.id)
            print(report.summary)

    asyncio.run(main())
    ```

## 📜 TypeScript SDK

The TypeScript SDK powers the CosmicSec Web Frontend and is ideal for custom security dashboards and integrations.

*   **Installation**: `npm install @cosmicsec/sdk`
*   **Usage**:
    ```typescript
    import { CosmicSecClient } from '@cosmicsec/sdk';

    const client = new CosmicSecClient({ apiKey: 'your-api-key' });

    async function run() {
      // Stream live SOC alerts via WebSocket
      client.soc.onAlert((alert) => {
        console.log(`New Alert: ${alert.title} (Severity: ${alert.severity})`);
      });
      
      // Query DeepIntel
      const intel = await client.deepintel.search('ransomware_group_x');
      console.log(intel.results);
    }
    ```

## 🏗️ Architecture

*   **Client Core**: Handles HTTP/WSS connections, headers, and error handling.
*   **Service Managers**: Specialized sub-clients for each module (e.g., `client.ai`, `client.scans`, `client.deepintel`).
*   **Models/Types**: Shared data definitions ensuring consistency between the SDK and the Backend APIs.
*   **Middleware**: Interceptors for logging, retry logic, and request tracing.

## 🔄 Integration Workflow

1.  **Initialize**: Provide an API Key or credentials to the `CosmicSecClient`.
2.  **Interact**: Use the service-specific methods to perform actions or query data.
3.  **Validate**: The SDK automatically validates response data against the platform schemas.
4.  **Listen**: For real-time data, subscribe to WebSocket events via the SDK listeners.

## 📂 File Structure

```text
cosmicsec-sdk/
├── sdk/
│   ├── python/          # Python SDK source (Pydantic models)
│   │   └── cosmicsec_sdk/
│   └── typescript/      # TypeScript SDK source (Zod types)
│       └── src/
├── tests/               # Cross-language integration tests
└── ...
```
