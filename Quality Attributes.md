## Quality Attribute Scenarios

| ID      | Attribute     | Scenario                                                                                            | Associated Use Case |
| ------- | ------------- | --------------------------------------------------------------------------------------------------- | ------------------- |
| **QA1** | Performance   | Under peak load (5,000 users), 95% of queries are answered within 2 seconds.                        | RS10                |
| **QA2** | Availability  | During a service failure, system restores full operation within 60 seconds.                         | RA6                 |
| **QA3** | Security      | Unauthorized users cannot access private student data; all access is logged.                        | RS7, RA5            |
| **QA4** | Modifiability | Adding a new AI service requires no downtime.                                                       | RM1, RM5            |
| **QA5** | Usability     | The chatbot UI can respond to a query in natural language and provide a clear menu of next options. | RS12                |
| **QA6** | Reliability   | If LMS connection fails, AIDAP retries within 5 seconds and logs an alert.                          | RD3                 |

---