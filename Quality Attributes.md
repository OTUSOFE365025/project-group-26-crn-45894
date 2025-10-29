## Quality Attribute Scenarios

| ID      | Attribute     | Scenario                                                                                            | Associated Use Case                              |
| ------- | ------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| **QA-1** | Performance   | Under peak load (5,000 users), 95% of queries are answered within 2 seconds.                        | **UC-1 — Ask Academic Questions**                |
| **QA-2** | Availability  | During a service failure, system restores full operation within 60 seconds.                         | **UC-8 — Monitor & Deploy Updates**              |
| **QA-3** | Security      | Unauthorized users cannot access private student data; all access is logged.                        | **UC-3 — Access Personalized Dashboard**         |
| **QA-4** | Modifiability | Adding a new AI service requires no downtime.                                                       | **UC-8 — Monitor & Deploy Updates**              |
| **QA-5** | Usability     | The chatbot UI can respond to a query in natural language and provide a clear menu of next options. | **UC-1 — Ask Academic Questions**                |
| **QA-6** | Reliability   | If LMS connection fails, AIDAP retries within 5 seconds and logs an alert.                          | **UC-9 — Synchronize Data**                      |
