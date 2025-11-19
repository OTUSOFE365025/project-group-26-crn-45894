# ADD Iteration 2 – Identifying Structures to Support Primary Functionality (AIDAP)

## 1. Iteration Goal and Drivers

The goal of this iteration is to refine the Iteration-1 structure into **domain-level components and interfaces** that support the primary AIDAP use cases and enable work allocation within the team.

Drivers for this iteration:

- **Primary Use Cases** (from Phase 1)
  - **UC-1:** Ask AIDAP a question
  - **UC-2:** Receive notifications
  - **UC-3:** View personalized dashboard
  - **UC-4:** Manage institutional integrations
  - **UC-5:** Maintain system health and configuration
- **Quality Attributes**
  - Performance (response time, throughput)
  - Availability and reliability
  - Modifiability (adding new external systems or AI models)
  - Security (correct enforcement of roles and data access)
- **Architectural Concerns**
  - **CRN-3:** Allocate work to team members based on components.
  - New concern **CRN-4:** Support automated unit/integration testing for most modules.

---

## 2. Step 3 – Elements to Refine

We refine the following elements defined in Iteration 1:

- **Conversation Orchestrator Service**
- **Notification Service**
- **Integration Services (LMS, Registration, Calendar, Email)**
- **User Profile & Personalization Service**
- **Analytics Service**
- **Client UIs (Chat UI and Dashboard UI)**

---

## 3. Step 4 – Design Concepts

The following design concepts are applied:

- **Domain Model Pattern** – Introduce domain objects that represent core AIDAP concepts (User, Course, ScheduleItem, ConversationSession, NotificationRule, IntegrationConfig, etc.).
- **Layered decomposition into modules/components** – Decompose each domain object into presentation, application, and data modules.
- **Facade pattern** – Expose simple service interfaces to clients (e.g., `ConversationAPI`, `NotificationAPI`, `AdminConfigAPI`).
- **Repository pattern** – Encapsulate persistence details behind repositories (e.g., `ConversationRepository`, `UserProfileRepository`).
- **Adapter pattern** – Wrap each external system’s API in an adapter (e.g., `LmsAdapter`, `RegistrationAdapter`).
- **Inversion of Control / Dependency Injection** – Connect components using a DI framework to support testing (**CRN-4**).

---

## 4. Step 5 – Instantiating Architectural Elements

### 4.1 Initial Domain Model

_Key domain entities (simplified textual view):_

- **User**
  - id, role (student, lecturer, admin, maintainer), name, email, locale, preferences.
- **ConversationSession**
  - id, userId, startTime, endTime, messages[], currentIntent, context.
- **Message**
  - timestamp, sender (user/system), text, structuredPayload.
- **NotificationRule**
  - id, userId or courseId, triggerType (deadline, announcement), channel (email, app), schedule.
- **ScheduleItem**
  - id, courseId, type (lecture, lab, exam, assignment), startTime, dueDate.
- **IntegrationConfig**
  - id, type (LMS, Registration, Calendar, Email), credentials, endpoint URLs, status.
- **UsageMetric**
  - timestamp, userRole, responseTime, success/failure, feature used.

You can draw the UML class diagram with these entities and commit it as an image or PlantUML file.

### 4.2 Mapping Use Cases to Domain Objects

| Use Case | Main Domain Objects |
|----------|---------------------|
| UC-1 Ask AIDAP a question | User, ConversationSession, Message, ScheduleItem, IntegrationConfig |
| UC-2 Receive notifications | User, NotificationRule, ScheduleItem, IntegrationConfig |
| UC-3 View personalized dashboard | User, ScheduleItem, UsageMetric |
| UC-4 Manage institutional integrations | IntegrationConfig |
| UC-5 Maintain system health | UsageMetric, IntegrationConfig |

### 4.3 Decomposition into Modules

#### Conversation and Q&A Flow (UC-1)

**Client Layer**

- `ChatView` – Renders chat messages and collects user input.
- `ChatController` – Sends user messages to backend and updates the view.

**Service Layer**

- `ConversationAPI` (facade)
  - `POST /conversations/{id}/messages`
  - `GET /conversations/{id}`
- `ConversationService`
  - Maintains conversation state, calls NLU/LLM, routes intents.
- `IntentRouter`
  - Maps identified intent to specific domain handlers (e.g., `ScheduleQueryHandler`, `GradesQueryHandler`).
- `ScheduleQueryHandler`
  - Queries LMS/Calendar integration for schedule-related answers.

**Data Layer**

- `ConversationRepository`
  - Persist and retrieve `ConversationSession` and `Message`.
- `UserProfileRepository`
  - Store user preferences and personalization data.

#### Notifications (UC-2)

**Service Layer**

- `NotificationAPI`
  - Manage notification rules (CRUD).
- `NotificationService`
  - Evaluates rules and enqueues notification jobs.
- `NotificationWorker`
  - Background worker that sends email or push notifications.

**Data Layer**

- `NotificationRuleRepository`
- `NotificationLogRepository`

#### Integrations (UC-4)

**Service Layer**

- `AdminConfigAPI`
  - Endpoints to configure integrations and policies.
- `IntegrationRegistry`
  - Knows which adapters are available and how to route requests.

**Data Layer**

- `IntegrationConfigRepository`

**Adapter Layer**

- `LmsAdapter`
- `RegistrationAdapter`
- `CalendarAdapter`
- `EmailAdapter`

Each adapter exposes a uniform interface (e.g., `getSchedule(userId)`, `getAnnouncements(courseId)`), hiding vendor-specific details.

#### Monitoring and Analytics (UC-5)

- `MonitoringAgent`
  - Exports metrics to the monitoring stack.
- `AnalyticsService`
  - Aggregates `UsageMetric` data for dashboards.

---

## 5. Step 6 – Interfaces and Example Flows

### 5.1 Example Sequence – UC-1: Ask AIDAP “When is my next exam?”

_Textual sequence:_

1. **Student** types question in `ChatView`.
2. `ChatController` calls `ConversationAPI.postMessage(conversationId, messagePayload)`.
3. `ConversationAPI` forwards to `ConversationService.handleMessage(...)`.
4. `ConversationService`:
   - Stores message via `ConversationRepository.saveMessage(...)`.
   - Calls NLU/LLM to identify intent `NextExamQuery`.
   - Delegates to `ScheduleQueryHandler.handleNextExam(userId, context)`.
5. `ScheduleQueryHandler`:
   - Calls `LmsAdapter.getUpcomingAssessments(userId)` and/or `CalendarAdapter.getEvents(userId)`.
   - Selects the next exam from the returned data.
6. `ConversationService` composes a natural-language answer and stores the system message.
7. `ConversationAPI` returns the answer to `ChatController`, which updates `ChatView`.

### 5.2 Key Service Interfaces (sketched as method signatures)

```
interface ConversationAPI {
    Answer postMessage(ConversationId id, MessagePayload payload);
    ConversationSession getConversation(ConversationId id);
}

interface ConversationService {
    Answer handleMessage(UserId user, MessagePayload payload);
}

interface ScheduleQueryHandler {
    ExamInfo findNextExam(UserId user, ConversationContext ctx);
}

interface LmsAdapter {
    List<Assessment> getUpcomingAssessments(UserId user);
}

interface CalendarAdapter {
    List<ScheduleItem> getEvents(UserId user);
}
```

### 5.3 Example Sequence – UC-2: Notification for Assignment Deadline

1. `NotificationService` periodically queries `ScheduleItem` data via **LmsAdapter** and **CalendarAdapter**.  
2. For each upcoming deadline matching a user's **NotificationRule**, it creates a job in the **Message Queue**.  
3. `NotificationWorker` consumes jobs, formats notification messages, and dispatches via **EmailAdapter** or push notification channel.  
4. Sent notifications are recorded in `NotificationLogRepository`.  
5. Dashboard UI retrieves logs for display via `AnalyticsService`.

*A full UML sequence diagram is included in `/phase2/diagrams/sequence_uc2_iter2.puml`.*

# 6. Step 7 – Analysis and Work Allocation

## 6.1 Driver Coverage

| Driver | Status | Notes |
|--------|--------|-------|
| **UC-1 Ask question** | Mostly addressed | Domain model, modules, and full flow defined. |
| **UC-2 Notifications** | Mostly addressed | Rules, services, adapters, and worker defined. |
| **UC-3 Dashboard** | Partially addressed | Analytics services identified; UI left for later iterations. |
| **UC-4 Manage integrations** | Mostly addressed | IntegrationConfig + adapters fully defined. |
| **UC-5 System health** | Partially addressed | Monitoring & analytics modules identified. |
| **Performance & scalability** | Partially addressed | Stateless services + adapters support scaling; caching/TLS tuning later. |
| **Modifiability** | Well supported | Adapter pattern enables new integrations/models easily. |
| **Security** | Partially addressed | Auth & Access Control assumed; detailed RBAC in later iteration. |
| **CRN-3 Work allocation** | Addressed | Each module can be assigned to a separate team member. |
| **CRN-4 Testability** | Partially addressed | Repositories and interfaces support testing; full test strategy comes later. |