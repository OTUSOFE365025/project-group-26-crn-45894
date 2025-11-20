# ADD Iteration 1 – Establishing Overall System Structure (AIDAP)

## 1. Design Purpose

This iteration defines the initial architectural structure of the **AI-Powered Digital Assistant Platform (AIDAP)**. The goal is to move from the Phase-1 requirements (use cases, quality attributes, constraints, and concerns) to a high-level architecture that can support conversational access to institutional data (LMS, registration, calendars, email, etc.).

## 2. Selected Drivers for Iteration 1

From Phase 1, the key drivers considered in this iteration are:

### Primary Functional Requirements (Use Cases)

- **UC-1: Ask AIDAP a question** – Student/lecturer/admin asks a natural-language question and receives an answer based on institutional data.
- **UC-2: Receive notifications** – AIDAP pushes deadline / announcement notifications to users.
- **UC-3: View personalized dashboard** – Student sees upcoming events and performance indicators.
- **UC-4: Manage institutional integrations** – Admin configures LMS/registration/calendar/email integrations.
- **UC-5: Maintain system health** – Maintainer monitors AIDAP health, latency, and errors.

(IDs should match the use case model from Phase 1.)

### Quality Attribute Scenarios

- **Performance (High Priority)** – AIDAP should respond to typical queries within **2 seconds** on average (RS10, QA-P1).
- **Availability (High Priority)** – AIDAP should be available **99.5%** of the time per month and support automatic fail-over (RS11, RA6, QA-A1).
- **Scalability (Medium Priority)** – Support up to **5,000 concurrent users** (RA7, QA-S1).
- **Security (High Priority)** – Protect user data, enforce SSO, and ensure only authorized access to personal and course data (R8, RS7, RS8, RA5, QA-Sec1).
- **Usability (Medium Priority)** – Provide an intuitive conversational UI usable by non-technical users (RS12, QA-U1).

### Constraints

- **CON-1:** AIDAP must integrate with existing **LMS, registration, calendar, and email** systems via standard APIs.
- **CON-2:** The system must be deployable as a **cloud-native service**.
- **CON-3:** Must comply with institutional privacy and security policies.
- **CON-4:** Must support **web, mobile, and voice-assistant** clients (RS9).
- **CON-5:** Average response time ≤ 2 seconds under normal load (RS10).

### Architectural Concerns

- **CRN-1:** Establish a clear overall structure that separates conversational logic, data access, and external integrations.
- **CRN-2:** Allow future integration of new AI models and external systems.
- **CRN-3:** Organize the architecture so work can be partitioned across team members.

---

## 3. Step 3 – Element to Refine

This is a greenfield architecture. The element being refined is the **entire AIDAP system**, in the context depicted below:

- **Actors**
  - Students (S)
  - Lecturers (L)
  - Administrators (A)
  - System Maintainers (M)
- **External Systems**
  - LMS, Registration, Calendar, Email, Analytics/Data warehouse

> _Context Diagram (to be provided as a separate UML/PlantUML figure in the repo)._  
> AIDAP sits between users and institutional systems, exposing a conversational API.

---

## 4. Step 4 – Design Concepts and Architectural Decisions

### 4.1 Reference Architectures

| Design Decision                                                                 | Rationale                                                                                                                                                                                     | Discarded Alternatives                                                                                 |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Use a Service Application reference architecture for the AIDAP backend.**     | Fits a cloud-native, stateless, API-driven backend that exposes services to multiple clients. Supports scalability and integration with institutional systems.                                | Monolithic desktop application – cannot satisfy multi-channel access and cloud deployment constraints. |
| **Use a Web / Rich Internet Application architecture for user-facing clients.** | Allows students, lecturers, and admins to use AIDAP through browsers and mobile apps while still providing rich, responsive UIs (chat window, dashboards).                                    | Traditional server-side web pages only – would limit responsiveness and conversational UX.             |
| **Use a 3-tier deployment pattern (client, application, data).**                | Clean separation of concerns: UI layer, application/AI layer, and persistence/integration layer. Supports availability and scalability via independent scaling of application and data tiers. | 2-tier architecture – would mix business logic with data access and reduce scalability.                |

### 4.2 Cross-Cutting Concepts

- **API Gateway / Load Balancer** for routing external traffic to AIDAP services and supporting scaling and fail-over.
- **Message Queue** for asynchronous tasks such as notification delivery and scheduled jobs.
- **Security and Logging Components** as cross-cutting modules shared by services.

---

## 5. Step 5 – Instantiate Architectural Elements and Allocate Responsibilities

Based on the reference architectures and patterns, the main **logical layers** are:

### Client Layer

- **Web / Mobile Chat UI**
  - Presents conversational interface (chat box, dashboard widgets).
  - Handles input validation and basic client-side rendering.
- **Admin & Maintainer Dashboards**
  - UI for configuration of integrations, policies, and monitoring.

### Application / Service Layer

- **Conversation Orchestrator Service**
  - Manages dialog state, calls NLU / LLM models, and routes intents to domain services.
- **User Profile & Personalization Service**
  - Stores preferences, language, and historical interactions (R2, RS5, RS6).
- **Notification Service**
  - Schedules and sends deadline/announcement notifications (RS2, RL4, RA3).
- **Integration Services**
  - LMS Adapter, Registration Adapter, Calendar Adapter, Email Adapter. Each encapsulates APIs of a specific external system.
- **Analytics & Reporting Service**
  - Provides aggregated usage/academic analytics to lecturers and administrators (RL3, RL6, RA4).
- **Auth & Access Control Service**
  - Handles SSO integration, roles (student/lecturer/admin/maintainer), and access checks (RS7, RS8, RL8, RA5).

### Data & Infrastructure Layer

- **AIDAP Database**
  - Stores user profiles, conversation history, notification schedules, configuration data.
- **Integration Connectors**
  - Libraries/clients used by Integration Services to communicate with LMS, registration, calendar, and email systems.
- **Message Broker**
  - Used by Notification and Analytics services for decoupled, asynchronous processing.
- **Monitoring & Logging Stack**
  - Metrics, logs, and traces for maintainers (RM2–RM4).

---

## 6. Step 6 – Views and Recorded Responsibilities

### 6.1 Module View (High-Level)

You will provide the UML diagram in the repo; below is the textual mapping.

| Module                                            | Layer         | Responsibility                                                                                                    |
| ------------------------------------------------- | ------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Chat UI**                                       | Client        | Chat interface for students/lecturers/admins; renders responses and suggestions.                                  |
| **Dashboard UI**                                  | Client        | Displays upcoming events, analytics, and configuration UIs.                                                       |
| **Conversation Orchestrator**                     | Service       | Receives user utterances, calls NLU/LLM, determines intent, and coordinates with domain services.                 |
| **User Profile Service**                          | Service       | CRUD for preferences, languages, and personalization data.                                                        |
| **Notification Service**                          | Service       | Manages notification rules, schedules jobs, and sends notifications via email/app.                                |
| **Integration Services (LMS/Reg/Calendar/Email)** | Service       | Encapsulate API calls to external university systems; translate domain-level requests into system-specific calls. |
| **Analytics Service**                             | Service       | Aggregates and exposes usage and performance analytics.                                                           |
| **Auth & Access Control**                         | Cross-cutting | Enforces SSO, user roles, and data access control across services.                                                |
| **Persistence / Repository Modules**              | Data          | Implement data access for conversations, users, configuration, and logs.                                          |
| **Monitoring & Logging**                          | Cross-cutting | Collects metrics and logs for maintainers.                                                                        |

### 6.2 Deployment View (High-Level)

Target cloud deployment (e.g., Kubernetes or managed services):

- **Client Tier**
  - Browser & mobile apps calling REST/GraphQL and WebSocket APIs.
- **Application Tier**
  - AIDAP services (conversation, profile, notification, integration, analytics) running in containers behind a load balancer/API gateway.
- **Data Tier**
  - Managed relational or document database.
  - Message broker (e.g., cloud queue service).
  - External university systems (LMS, registration, calendar, email) as external nodes.

---

## 7. Step 7 – Analysis of Current Design

Summary of how the iteration addressed drivers (similar to the FCAPS Kanban table):

| Driver                                  | Status                   | Notes                                                                                                                 |
| --------------------------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| UC-1 Ask question                       | **Partially Addressed**  | Conversation Orchestrator and client UIs identified; detailed flows scheduled for Iteration 2.                         |
| UC-2 Notifications                      | **Partially Addressed**  | Notification Service and message queue identified; implementation details to follow.                                   |
| UC-3 Dashboard                          | **Not Addressed**        | Dashboard UI and Analytics Service identified conceptually; no implementation yet.                                     |
| UC-4 Manage integrations                | **Partially Addressed**  | Integration Services and Admin UI identified; integration patterns to be refined in Iteration 2.                       |
| UC-5 Maintain system                    | **Not Addressed**        | Monitoring & Logging stack identified but not implemented.                                                            |
| Performance (≤ 2s)                      | **Partially Addressed**  | 3-tier structure supports scaling; detailed performance tactics to be implemented.                                    |
| Availability (99.5%)                    | **Partially Addressed**  | Cloud deployment planned; replication and fail-over to be implemented later.                                         |
| Security & privacy                      | **Partially Addressed**  | Auth & Access Control service defined; enforcement pending.                                                           |
| Scalability                             | **Completely Addressed** | Service-based architecture and stateless design support horizontal scaling.                                           |
| Usability                               | **Not Addressed**        | Rich web/mobile client layer identified; detailed UI/UX design not started.                                          |
| Constraints (integration, cloud-native) | **Completely Addressed** | External integration services and cloud deployment pattern selected.                                                  |
| Concerns CRN-1/CRN-2/CRN-3              | **Partially Addressed**  | Overall structure established; detailed module assignments pending.                                                  |


The main outcome of Iteration 1 is a coherent high-level architecture that can now be refined into domain-level components and interfaces in Iteration 2.
