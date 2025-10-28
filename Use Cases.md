## Business Case

The **AI-Powered Digital Assistant Platform (AIDAP)** is a conversational AI system designed to enhance access to academic and institutional information for students, lecturers, and administrators. The system integrates with existing university services such as the Learning Management System (LMS), registration databases, calendars, and email servers to provide contextual, personalized responses to queries.

**Key goals:**

- Improve information accessibility and reduce administrative overhead.
- Deliver personalized insights (e.g., deadlines, grades, events) via chat or voice.
- Support both web and mobile platforms with 99.5% availability.
- Ensure compliance with institutional privacy and security standards.

The business value lies in streamlining academic interactions, increasing operational efficiency, and improving the student and faculty experience through intelligent automation.

---

## Stakeholders and System Overview

| Symbol | Stakeholder         | Description                                                              |
| ------ | ------------------- | ------------------------------------------------------------------------ |
| **S**  | Students            | End users who query academic and campus information.                     |
| **L**  | Lecturers           | Provide course-related content and respond to academic queries.          |
| **A**  | Administrators      | Maintain institutional data, integrations, and policies.                 |
| **M**  | System Maintainer   | Responsible for deployment, monitoring, and upgrades.                    |
| **D**  | Data Source Systems | External systems such as LMS, registration, calendar, and email servers. |

---

## Use Case Model

### Use Case Diagram

![alt text](use_case_diagram.png)
```plantuml
@startuml
left to right direction
actor Student as S
actor Lecturer as L
actor Administrator as A
actor Maintainer as M
actor "Data Source Systems" as D

rectangle AIDAP {
  usecase "UC-1: Ask Academic Questions" as UC1 #LightBlue
  usecase "UC-2: Receive Notifications" as UC2
  usecase "Access Personalized Dashboard" as UC3
  usecase "Publish Course Material" as UC4
  usecase "Post Announcements" as UC5
  usecase "View Analytics" as UC6
  usecase "Manage Integrations" as UC7
  usecase "Monitor & Deploy Updates" as UC8
  usecase "Synchronize Data" as UC9
}

S --> UC1
S --> UC2
S --> UC3
L --> UC4
L --> UC5
L --> UC6
A --> UC7
M --> UC8
D --> UC9
@enduml
```

### Use Case Descriptions

| Use Case ID | Use Case                      | ID Reference  | Description                                                | Primary Actor       |
| ----------- | ----------------------------- | ------------- | ---------------------------------------------------------- | ------------------- |
| UC-1        | Ask Academic Questions        | RS1, R1, R5   | Student queries AIDAP for schedule, grades, or exam dates. | Student             |
| UC-2        | Receive Notifications         | RS2, RS13     | System sends alerts for announcements or due dates.        | Student             |
| UC-3        | Access Personalized Dashboard | RS3, RS5      | Students view upcoming deadlines, grades, and events.      | Student             |
| UC-4        | Publish Course Material       | RL1           | Lecturers upload or update course files via the assistant. | Lecturer            |
| UC-5        | Post Announcements            | RL2, RS2      | Lecturers broadcast messages to students.                  | Lecturer            |
| UC-6        | View Analytics                | RL3, RL6      | View attendance, grades, and engagement summaries.         | Lecturer            |
| UC-7        | Manage Integrations           | RA1, RA2      | Admin configures LMS, registration, and calendar links.    | Administrator       |
| UC-8        | Monitor & Deploy Updates      | RM1, RM2      | Maintainers monitor performance and deploy updates.        | Maintainer          |
| UC-9        | Synchronize Data              | RD1, RD2, RD3 | Data from external systems synchronized periodically.      | Data Source Systems |

---

## Stakeholder Context Diagram


```plantuml
@startuml
actor Student as S
actor Lecturer as L
actor Administrator as A
actor Maintainer as M
actor "External Systems" as D

rectangle "AI-Powered Digital Assistant Platform (AIDAP)" {
  [Conversational Interface]
  [AI Query Processor]
  [Personalization Engine]
  [Data Integration Services]
  [Monitoring Dashboard]
}

S --> [Conversational Interface]
L --> [Conversational Interface]
A --> [Monitoring Dashboard]
M --> [Monitoring Dashboard]
[Conversational Interface] --> [AI Query Processor]
[AI Query Processor] --> [Data Integration Services]
[AI Query Processor] --> [Personalization Engine]
[Data Integration Services] --> D
@enduml
```

---

