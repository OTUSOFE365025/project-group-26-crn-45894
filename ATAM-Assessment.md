# ATAM Assessment – AIDAP Phase 3

This document contains the ATAM-style assessment of the AIDAP architecture after ADD Iteration 3.

---

## 1. Utility Tree

### 1.0 Utility Tree Diagram (ATAM style)

```
Utility
├── Performance
│   ├── Query Latency
│   │   ├── (H,M) Student receives answer to "When is my next exam?" in < 2s for 95% of requests
│   │   └── (H,H) 5,000 concurrent users: 90% of responses < 2s
├── Availability
│   ├── Node/Service Failure
│   │   ├── (H,H) Node crash: system recovers in < 30s, no data loss
│   │   └── (M,M) Restart after disk failure in < 5 min
│   └── External System Failure
│       └── (M,M) LMS down: user notified in < 2s, no hang
├── Security & Privacy
│   ├── Data Confidentiality
│   │   └── (H,M) Student cannot access another's grades; blocked & logged
│   └── Auth/Token Management
│       └── (H,M) Expired/revoked SSO token blocks further requests until re-login
├── Usability
│   ├── Learnability
│   │   └── (M,M) First-time student gets exam schedule in ≤ 2 turns
│   └── Error Handling
│       └── (M,M) Ambiguous question: AIDAP prompts for clarification
└── Modifiability
  ├── Integrate New System
  │   └── (M,H) Add Student Analytics adapter, no core changes, 1 sprint
  └── Swap LLM Model
    └── (M,M) Adopt new LLM via config + blue/green deploy, zero downtime
```

### 1.1 Top-Level Quality Attributes

- Performance
- Availability
- Security & Privacy
- Usability
- Modifiability

### 1.2 Leaf Scenarios

| QA                 | ID     | Scenario                                                                                                                                                                    | Importance | Difficulty |
| ------------------ | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | ---------- |
| Performance        | **P1** | Under normal load a student asks “When is my next exam?” and receives a correct answer within **2 s** for at least 95% of requests.                                         | High       | Medium     |
| Performance        | **P2** | During exam week up to **5,000 concurrent users** send queries; 90% of responses still meet the 2 s target.                                                                 | High       | High       |
| Availability       | **A1** | One AIDAP app node crashes; the system recovers automatically within **30 s** with no data loss.                                                                            | High       | High       |
| Availability       | **A2** | The LMS is down; AIDAP informs the user the LMS is unavailable within 2 s and does not hang.                                                                                | Medium     | Medium     |
| Security & Privacy | **S1** | A student attempts to access another student’s grades; the request is blocked, logged, and no data is leaked.                                                               | High       | Medium     |
| Security & Privacy | **S2** | An SSO token expires or is revoked; subsequent AIDAP requests are rejected until the user signs in again.                                                                   | High       | Medium     |
| Usability          | **U1** | A first-time student, with no training, successfully obtains their exam schedule in at most two turns.                                                                      | Medium     | Medium     |
| Usability          | **U2** | When the student asks an ambiguous question (“What’s going on tomorrow?”), AIDAP responds quickly with a clarification prompt.                                              | Medium     | Medium     |
| Modifiability      | **M1** | A new “Student Analytics” system is integrated by adding an adapter; no changes are required in ChatView or ConversationService, and the feature is released in one sprint. | Medium     | High       |
| Modifiability      | **M2** | A new LLM model is adopted by updating configuration and rolling out via blue-green deployment with zero downtime.                                                          | Medium     | Medium     |

---

## 2. ATAM Risk Assessment


### 2.1 Summary Table

| Analysing Scenario | P2.1 |
|--------------------|-----------------------------|
| Scenario           | 5,000 concurrent users send queries |
| Attributes         | Performance |
| Stimulus           | Surge in user queries during exam week |
| Environment        | Normal operation, peak load |
| Response           | 90% of responses < 2s; autoscaling and DB must keep up |

| Architecture Decision         | Sensitivity | Tradeoff | Risk | Nonrisk |
|------------------------------|-------------|----------|------|---------|
| AD1 Microservices + K8s      | R6          | R7       | R1   | R2      |
| AD2 API Gateway + OIDC SSO   | R6, R8      | R8       | R3   | R5      |
| AD3 Vector DB + RAG          | R6          |          |      |         |
| AD4 Kafka for async workflows|             | R7       |      |         |


---

## 3. Narrative Interpretation

### 3.1 Risks

- **R1:** Performance under peak load  
  Load-balancing and replication are in place, but without careful capacity planning, autoscaling, and DB tuning, the system may fail to meet P2 during exam peaks.

- **R2:** Data privacy enforcement  
  Authorization must be implemented in a single, consistent place (ConversationService or API layer). If adapters perform their own checks inconsistently, bugs can undermine S1.

- **R3:** Integration modifiability  
  If current design bypasses adapters and accesses external systems directly, integrating a new analytics system will require refactoring, increasing cost and schedule risk.

### 3.2 Non-Risks

- **N1:** Node failure handling  
  Because app nodes are stateless and behind a load balancer, losing a node only reduces capacity slightly. Health checks and replication support A1.

- **N2:** Updating the LLM  
  Since the AI model is wrapped by a dedicated adapter and selected via configuration, switching to a new model is low-risk from both code and availability perspectives.

### 3.3 Sensitivity Points

- **S1:** End-to-end latency  
  Response time depends strongly on:
  - Cache hit rate and TTL settings.
  - Whether calls to LMS and Calendar are parallelized.
  - DB replica performance.  
    These are parameters to be tuned using monitoring data.

### 3.4 Tradeoff Points

- **T1:** Retry vs responsiveness  
  A more aggressive retry policy improves robustness when LMS is flaky but can make AIDAP responses slower or contribute to thundering-herd effects after outages.

- **T2:** Security vs overhead  
  Very strict token validation and logging improve security but add CPU and latency overhead. Some caching of token checks is acceptable if aligned with institutional security policy.

---

## 4. Conclusion

The ATAM analysis shows that:

- The design is strong in fail-over and in supporting LLM evolution.
- The main open risks relate to **scalability**, **authorization correctness**, and **integration modifiability**.
- Several sensitivity and tradeoff points (caching, retries, token validation) must be tuned based on production metrics.

These findings can be used to prioritize future implementation and testing tasks in Phase 4 (if the project continued beyond this course).
