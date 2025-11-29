# ADD Iteration 3 – Quality Attribute–Driven Refinement

## Step 2 – Establish Iteration Goal and Drivers

**Iteration goal**

Refine the AIDAP architecture from Iterations 1 and 2 to prioritize three key quality attributes needed for a production deployment:

- **Performance** – answer most queries within 2 seconds on average (RS10).
- **Availability** – maintain at least 99.5% uptime with fail-over support (RS11, RA6).
- **Security & Privacy** – enforce SSO, RBAC, and protection of student data (R8, RS7, RS8, RA5).

These are high-priority business drivers because AIDAP will be exposed to all students, instructors, and admins and must remain responsive, reliable, and safe.

---

## Step 3 – Choose Elements to Refine

We refine the following elements from earlier iterations:

- **Client / Presentation**
  - `ChatView`, `ChatController` in the web UI.
- **Application / Service layer**
  - `ConversationAPI`, `ConversationService`
  - `ScheduleQueryHandler`, other handlers
  - `LmsAdapter`, `CalendarAdapter`, `RegistrationAdapter`, `EmailAdapter`
  - `NluAdapter` / AI model integration
  - `NotificationService`
- **Infrastructure**
  - API Gateway / Load Balancer
  - Application server nodes
  - Databases (user data, conversations, analytics)
  - Caching layer
  - Message broker / queue
  - Monitoring & logging
  - Auth / SSO integration

These elements have the biggest impact on performance, availability, and security for typical queries such as _“When is my next exam?”_ and for deadline notifications.

**Rationale for choosing these elements:**  
These components were selected because they directly affect the three quality attributes driving this iteration—**performance**, **availability**, and **security/privacy**. The API Gateway, App Nodes, and Cache are central to meeting the 2-second response-time requirement. The replicated App Nodes, Message Broker, and DB Replicas are essential for achieving high availability and fault tolerance. The SSO Adapter, Audit Logger, and ConversationService are the primary enforcement points for authentication, authorization, and privacy constraints. These elements therefore represent the parts of the architecture most impacted by the required quality improvements and are the natural focus of refinement in this iteration.

---

## Step 4 – Design Decisions and Tactics Used

| Design Decisions and Location | Rationale and Assumptions |
|------------------------------|---------------------------|
| **Replicate AIDAP App Nodes using active redundancy** | Replicating the application nodes allows the system to tolerate failures of a single node without service interruption. This supports availability requirements (RS11, RA6). Load can also be distributed to improve performance under peak usage. |
| **Introduce a load-balancing mechanism at the API Gateway** | A load balancer distributes traffic evenly across replicated App Nodes, improving responsiveness and ensuring no single node becomes a bottleneck. Technology-supported load balancing avoids custom solutions and increases reliability. |
| **Deploy a Message Broker on a separate node** | Separating the notification pipeline ensures that failure of the main application nodes does not interfere with delivery of notifications or background tasks. A queue also ensures ordered and reliable message processing. |
| **Use a DB Primary + Read Replica deployment strategy** | The read replica improves performance by offloading read-heavy workloads, while the primary handles writes. Replication supports fail-over and improves availability. |
| **Introduce a Conversation Cache (Redis)** | Frequently accessed data such as upcoming assessments or recent conversation context can be cached to reduce latency and meet the 2-second response-time requirement. |
| **Enforce SSO authentication at the Gateway boundary** | Ensuring authentication occurs at the perimeter prevents unauthorized access and reduces the risk of bypassing access controls. This strengthens security/privacy compliance. |
| **Add a Security Audit Logger** | Logging access attempts, SSO validations, and sensitive data interactions supports auditing requirements and detection of anomalies. Assumes sufficient storage and log rotation policies. |
| **Integrate Monitoring & Alerting** | Provides continuous observation of latency, failure rates, node health, and external API availability. Needed to validate that performance and availability requirements are being met in real time. |

---

## Step 5 – Instantiate Architectural Elements

| Element                           | Type               | New / Refined Responsibilities                                                                                  |
| --------------------------------- | ------------------ | --------------------------------------------------------------------------------------------------------------- |
| **API Gateway / Load Balancer**   | Infrastructure     | TLS termination, SSO redirect, token validation, routing to app nodes, health checks.                           |
| **AIDAP App Node (replicated)**   | Application server | Hosts ConversationAPI / ConversationService / handlers / adapters. Stateless; can be scaled horizontally.       |
| **Conversation Cache**            | In-memory cache    | Stores hot user data (schedule, upcoming assessments, short-term history) with TTL to meet 2s latency.          |
| **Message Broker / Queue**        | Infrastructure     | Buffers background work such as deadline notifications and bulk announcements so interactive queries stay fast. |
| **User DB – Primary**             | Database           | Handles all writes: user profiles, preferences, conversation logs. Backed up regularly.                         |
| **User DB – Read Replica**        | Database           | Serves read-heavy queries from ConversationService and analytics to reduce latency and improve availability.    |
| **Monitoring & Alerting Service** | Infrastructure     | Collects metrics (latency, throughput, error rate, CPU, memory) and sends alerts when SLOs are violated.        |
| **Auth / SSO Adapter**            | Service component  | Talks to institutional SSO, validates tokens, maps them to AIDAP roles and user IDs.                            |
| **Security Audit Logger**         | Service component  | Logs security-relevant actions (login, failed access, admin operations) for compliance and debugging.           |

---

## Step 6 – Refined Views

### 6.1 Refined deployment (textual)

1. A student uses a browser or mobile client to access the AIDAP chat UI.
2. The browser connects over HTTPS to the **API Gateway / Load Balancer**.
3. The gateway validates SSO tokens and forwards the request to one of several **AIDAP App Nodes**.
4. The app node:
   - Reads/writes data from the **User DB primary/replica**.
   - Uses the **Conversation Cache** for hot data.
   - Calls external systems (LMS, Calendar) via adapters, usually in parallel.
   - Enqueues long-running work on the **Message Broker** for NotificationService.
5. All components emit metrics to **Monitoring & Alerting** and security events to **Security Audit Logger**.

A PlantUML deployment diagram for this is provided in `diagrams/aidap-phase3-deployment.puml`.

---

## Step 7 – Analyze How the Design Meets the Drivers

| Driver                                                 | Status                  | Rationale                                                                                                                                                              |
| ------------------------------------------------------ | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Performance – 2s response time**                     | **Partially satisfied** | Caching, parallel calls, and DB replicas reduce latency for common queries, but actual 2s SLO must be validated by load testing and tuning.                            |
| **Performance – scalability (5,000 concurrent users)** | **Partially satisfied** | Stateless replicated app nodes, load balancer, and message broker support horizontal scaling; requires autoscaling and capacity planning.                              |
| **Availability – 99.5% uptime and fail-over**          | **Partially satisfied** | Redundant app nodes and DB replicas with health checks and backups support high availability. Detailed fail-over runbooks are still needed.                            |
| **Security & Privacy – SSO, RBAC, data protection**    | **Partially satisfied** | Perimeter SSO, RBAC in ConversationService, encryption, and audit logging address core concerns; a formal threat model and penetration testing would be the next step. |
| **Modifiability – evolving AIDAP**                     | **Partially satisfied** | Adapters and separation between UI, services, and external systems allow new data sources or NLU models to be added with limited changes.                              |

**Conclusion:** Iteration 3 strengthens the architecture for real-world deployment by explicitly focusing on performance, availability, and security, while preserving the structure defined in Iterations 1 and 2.
