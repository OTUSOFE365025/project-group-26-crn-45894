## Quality Attribute Scenarios

| ID      | Attribute     | Scenario                                                                                            | Associated Use Case                             |
| ------- | ------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| **QA1** | Performance   | Under peak load (5,000 users), 95% of queries are answered within 2 seconds.                        | **C1 – Handle User Query**                      |
| **QA2** | Availability  | During a service failure, system restores full operation within 60 seconds.                         | **C2 – System Recovery / Failover**             |
| **QA3** | Security      | Unauthorized users cannot access private student data; all access is logged.                        | **C3 – Authenticate and Authorize Users**       |
| **QA4** | Modifiability | Adding a new AI service requires no downtime.                                                       | **C4 – Integrate or Update AI Model**           |
| **QA5** | Usability     | The chatbot UI can respond to a query in natural language and provide a clear menu of next options. | **C5 – User Interaction / Conversational Flow** |
| **QA6** | Reliability   | If LMS connection fails, AIDAP retries within 5 seconds and logs an alert.                          | **C6 – Synchronize with External Systems**      |
