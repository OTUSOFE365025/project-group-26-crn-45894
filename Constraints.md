# System Constraints — AIDAP

List of constraints that must be respected by the architecture and implementation.

1. Cloud-native deployment: Containerized microservices with CI/CD pipelines and capability for zero-downtime deployment (R7, RM1).
2. Institutional SSO: Mandatory integration with institutional Single Sign-On (OIDC or SAML) for all authentication (RS7).
3. Standard APIs: External integrations must use REST or GraphQL; connectors must implement a standard adapter interface (RD2).
4. Data privacy & residency: Must comply with institutional and legal policies; encryption in transit and at rest is required (RA5, R8).
5. Performance SLA: Average response time ≤ 2s under normal load; provide measurable targets for 95th/99th percentiles (RS10).
6. Availability SLA: System uptime ≥ 99.5% per calendar month (RS11, RA6).
7. Concurrency target: Handle up to 5,000 concurrent users with autoscaling and capacity planning (RA7).
8. Retention policies: Interaction history persisted with configurable retention controlled by administrators (R2, RA2).
9. Interaction modes: Support text and voice interactions; client-side offline cache for recent responses for limited connectivity (R4, RS14).
10. Model management: Support versioned AI models, configurable API keys, and controlled rollouts (RM3).
11. Observability: Centralized logs, metrics, and tracing must be available for maintainers (RM2).
12. Cost constraints: Deploy with cost awareness; design autoscaling and model selection to balance latency and cloud spend.
