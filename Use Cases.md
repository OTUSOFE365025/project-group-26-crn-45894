```markdown
Add the Use Cases to this file

```
```markdown
# Use Cases — AI‑Powered Digital Assistant Platform (AIDAP)

Actors
- Student (S)
- Lecturer (L)
- Administrator (A)
- System Maintainer (M)
- External Data Source (D)

Primary Use Cases (detailed)

1. UC-QueryAcademicInfo
- Actors: Student, Lecturer
- Goal: Ask a natural-language academic or administrative question (e.g., "When is my next exam?")
- Preconditions: User authenticated via institutional SSO
- Main Flow:
	1. User submits query (text or voice).
	2. Frontend forwards request to API Gateway (auth check).
	3. NLU/Intent service extracts intent and entities.
	4. Planner determines data sources (live connectors or personal store).
	5. Data fetched and assembled; retrieval augmented generation used if required.
	6. Response produced and returned to user via chosen channel.
	7. Interaction logged to personalization store.
- Extensions:
	- If external data unavailable → present best-effort response and schedule follow-up.
	- If ambiguous intent → request disambiguation from user.
- Postconditions: Interaction recorded for personalization and analytics.
- Success guarantee: Relevant answer within SLA (RS10).
- Mapped requirements: RS1, R1, R5, R6, RS10, RS11

2. UC-ReceiveNotifications
- Actors: Student, Lecturer
- Goal: Receive notifications for deadlines, schedule changes, announcements.
- Main Flow:
	1. Scheduler or lecturer-triggered event creates notification.
	2. Notification service formats message according to user preferences.
	3. Deliver via push, email, in-app, or voice channel.
- Extensions: Delivery failure → retry/backoff; user unsubscribes → update preferences.
- Mapped: RS2, RS6, RL4

3. UC-PersonalDashboard
- Actors: Student
- Goal: View personalized dashboard of upcoming events and performance indicators.
- Main Flow:
	1. User requests dashboard.
	2. Aggregator fetches personalized data (calendar, grades, analytics).
	3. Frontend renders dashboard; allows export to calendar (RS13).
- Mapped: RS3, RS13

4. UC-PublishCourseMaterial
- Actors: Lecturer
- Goal: Publish or update course materials via UI or conversational command.
- Main Flow:
	1. Lecturer uploads/edits material.
	2. System validates permissions (RBAC) and stores artifact.
	3. Optionally notify students.
- Mapped: RL1, RL2, RL8

5. UC-AdminIntegrations
- Actors: Administrator
- Goal: Configure and manage external integrations (LMS, registration, calendar).
- Main Flow:
	1. Admin registers connector and credentials.
	2. System validates and schedules synchronization.
	3. Monitoring surfaces connector health.
- Mapped: RA1, RD1, RD2, RA2

6. UC-ManageDeployments
- Actors: System Maintainer
- Goal: Deploy updates and manage AI model versions with zero downtime.
- Main Flow:

	# Use Cases — AI‑Powered Digital Assistant Platform (AIDAP)

	Actors
	- Student (S)
	- Lecturer (L)
	- Administrator (A)
	- System Maintainer (M)
	- External Data Source (D)

	Primary Use Cases (detailed)

	1. UC-QueryAcademicInfo
	- Actors: Student, Lecturer
	- Goal: Ask a natural-language academic or administrative question (e.g., "When is my next exam?")
	- Preconditions: User authenticated via institutional SSO
	- Main Flow:
	  1. User submits query (text or voice).
	  2. Frontend forwards request to API Gateway (auth check).
	  3. NLU/Intent service extracts intent and entities.
	  4. Planner determines data sources (live connectors or personal store).
	  5. Data fetched and assembled; retrieval augmented generation used if required.
	  6. Response produced and returned to user via chosen channel.
	  7. Interaction logged to personalization store.
	- Extensions:
	  - If external data unavailable → present best-effort response and schedule follow-up.
	  - If ambiguous intent → request disambiguation from user.
	- Postconditions: Interaction recorded for personalization and analytics.
	- Success guarantee: Relevant answer within SLA (RS10).
	- Mapped requirements: RS1, R1, R5, R6, RS10, RS11

	2. UC-ReceiveNotifications
	- Actors: Student, Lecturer
	- Goal: Receive notifications for deadlines, schedule changes, announcements.
	- Main Flow:
	  1. Scheduler or lecturer-triggered event creates notification.
	  2. Notification service formats message according to user preferences.
	  3. Deliver via push, email, in-app, or voice channel.
	- Extensions: Delivery failure → retry/backoff; user unsubscribes → update preferences.
	- Mapped: RS2, RS6, RL4

	3. UC-PersonalDashboard
	- Actors: Student
	- Goal: View personalized dashboard of upcoming events and performance indicators.
	- Main Flow:
	  1. User requests dashboard.
	  2. Aggregator fetches personalized data (calendar, grades, analytics).
	  3. Frontend renders dashboard; allows export to calendar (RS13).
	- Mapped: RS3, RS13

	4. UC-PublishCourseMaterial
	- Actors: Lecturer
	- Goal: Publish or update course materials via UI or conversational command.
	- Main Flow:
	  1. Lecturer uploads/edits material.
	  2. System validates permissions (RBAC) and stores artifact.
	  3. Optionally notify students.
	- Mapped: RL1, RL2, RL8

	5. UC-AdminIntegrations
	- Actors: Administrator
	- Goal: Configure and manage external integrations (LMS, registration, calendar).
	- Main Flow:
	  1. Admin registers connector and credentials.
	  2. System validates and schedules synchronization.
	  3. Monitoring surfaces connector health.
	- Mapped: RA1, RD1, RD2, RA2

	6. UC-ManageDeployments
	- Actors: System Maintainer
	- Goal: Deploy updates and manage AI model versions with zero downtime.
	- Main Flow:
	  1. Maintainer pushes new image/config.
	  2. CI/CD pipeline performs rolling or blue/green deployment.
	  3. Model routing supports version selection and rollback.
	- Mapped: RM1, RM3

	7. UC-AuthAndAccessControl
	- Actors: All authenticated users
	- Goal: Authenticate via SSO and enforce RBAC and per-user visibility.
	- Main Flow:
	  1. User authenticates using institutional SSO (OIDC/SAML).
	  2. Token validated at gateway; RBAC enforced downstream.
	- Mapped: RS7, RS8, RA5

	Traceability sample
	- UC-QueryAcademicInfo → RS1, R1, R5, RS10, RS11
	- UC-ReceiveNotifications → RS2, RS6, RL4
	- UC-AdminIntegrations → RA1, RD1, RD2

	Notes
	- Add UML use-case diagram artifact in /diagrams for Deliverable 1.
