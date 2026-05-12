# Communication Patterns

CosmicSec uses a variety of communication protocols optimized for different workloads and performance requirements.

## 1. External APIs (Client-to-Gateway)

Clients (Web, CLI, SDK) primarily interact with the platform through the API Gateway using:

*   **REST (HTTP/JSON)**: For standard CRUD operations, configuration management, and resource queries.
*   **GraphQL**: Used by the Web Frontend for dashboard data aggregation, allowing clients to request exactly what they need in a single trip.
*   **WebSockets**: For real-time updates, including live scan progress, SOC alerts, and collaboration session events.

## 2. Internal Communication (Service-to-Service)

Internal services communicate using high-performance, secure channels:

*   **REST/mTLS**: Standard synchronous communication between services, secured with mutual TLS.
*   **gRPC**: Used for high-throughput, low-latency communication, specifically between the Rust Ingest service and the Python API Gateway.
*   **Kafka (Event Streaming)**: The primary mechanism for asynchronous communication. Services emit events (e.g., `SCAN_COMPLETED`) that other services can subscribe to.

## 3. Real-Time Collaboration

The `collab_service` manages real-time synchronization between multiple SOC analysts:

*   **WebSocket Engine**: Handles low-latency messaging and state synchronization.
*   **Conflict Resolution**: Uses Operational Transformation (OT) or CRDT patterns (depending on the resource) to handle concurrent edits to reports or investigation notes.

## 4. Discovery and Routing

*   **Service Discovery**: The `service_discovery` module in `cosmicsec-core` maintains a registry of active service instances and their health status.
*   **Hybrid Router**: A specialized middleware that intelligently routes requests based on protocol (REST vs GraphQL) and service health.

## 5. Protocol Summary

| Protocol | Usage | Security |
| :--- | :--- | :--- |
| **HTTPS (REST)** | External/Internal APIs | TLS 1.3 / JWT / mTLS |
| **GraphQL** | Frontend Data Aggregation | TLS 1.3 / JWT |
| **WebSockets** | Real-time Dashboards | WSS / JWT |
| **gRPC** | High-performance Ingest | mTLS |
| **Kafka** | Async Events / Audit Logs | SASL/SCRAM + TLS |
