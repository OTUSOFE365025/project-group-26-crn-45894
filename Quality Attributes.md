## Quality Attributes

| Attribute                     | Description                                     | Requirement ID | Example Measure/Scenario                                 |
| ----------------------------- | ----------------------------------------------- | -------------- | -------------------------------------------------------- |
| **Performance**               | Responses must be processed quickly.            | RS10           | Average response time ≤ 2s under normal load.            |
| **Availability**              | Service must remain accessible.                 | RS11, RA6      | 99.5% uptime per month with failover.                    |
| **Scalability**               | System must handle growth and spikes.           | RA7            | Up to 5,000 concurrent users.                            |
| **Security**                  | Protect user data and ensure secure access.     | RS7, RA5, RM7  | Single sign-on (SSO), encryption, and role-based access. |
| **Usability**                 | Interface must be intuitive and accessible.     | RS12           | Conversational UI meets accessibility standards.         |
| **Modifiability**             | Easy integration of new AI models or APIs.      | RM5            | Modular microservice design.                             |
| **Interoperability**          | Works with LMS, registration, and email.        | R3, RD2        | REST/GraphQL API interfaces.                             |
| **Reliability**               | Handles network or service failures gracefully. | RD3            | Automatic retries and recovery.                          |
| **Learnability/Adaptability** | Improves response relevance over time.          | RS5            | Adaptive AI model based on historical data.              |

### Quality Attribute Scenarios

| ID      | Attribute     | Scenario                                                                                            | Associated Use Case |
| ------- | ------------- | --------------------------------------------------------------------------------------------------- | ------------------- |
| **QA1** | Performance   | Under peak load (5,000 users), 95% of queries are answered within 2 seconds.                        | RS10                |
| **QA2** | Availability  | During a service failure, system restores full operation within 60 seconds.                         | RA6                 |
| **QA3** | Security      | Unauthorized users cannot access private student data; all access is logged.                        | RS7, RA5            |
| **QA4** | Modifiability | Adding a new AI service requires no downtime.                                                       | RM1, RM5            |
| **QA5** | Usability     | The chatbot UI can respond to a query in natural language and provide a clear menu of next options. | RS12                |
| **QA6** | Reliability   | If LMS connection fails, AIDAP retries within 5 seconds and logs an alert.                          | RD3                 |

---