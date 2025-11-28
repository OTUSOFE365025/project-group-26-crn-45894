# ATAM Assessment – AIDAP Phase 3

This document contains the ATAM-style assessment of the AIDAP architecture after ADD Iteration 3.

---

## 1. Utility Tree

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

| ID     | Scenario ID(s) | Category              | Description                                                                                                                                                                                |
| ------ | -------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **R1** | P2             | **Risk**              | The system may not meet the 2 s SLO at 5,000 concurrent users if autoscaling or DB capacity are insufficient.                                                                              |
| **R2** | A1             | **Non-Risk**          | Stateless replicated app nodes and DB replicas make recovery from a node crash straightforward.                                                                                            |
| **R3** | S1             | **Risk**              | If RBAC / data scoping are not enforced centrally, bugs in an adapter could expose another student’s data.                                                                                 |
| **R4** | M1             | **Risk**              | If ConversationService is tightly coupled to existing data sources, adding a new analytics service will require invasive changes and large regression effort.                              |
| **R5** | M2             | **Non-Risk**          | Configuration-driven model selection in NluAdapter means new LLM versions can be deployed with minimal code change and controlled rollout.                                                 |
| **R6** | P1             | **Sensitivity point** | Overall latency is highly sensitive to caching parameters and whether external calls are parallel or sequential. Small changes here can move the system in or out of compliance with RS10. |
| **R7** | A2             | **Tradeoff point**    | Aggressive retries towards LMS improve perceived availability but can increase latency and overload a recovering LMS. Retry and circuit-breaker settings balance these concerns.           |
| **R8** | S2             | **Tradeoff point**    | Validating tokens on every request strengthens security but adds per-request overhead; caching token validation improves performance but increases the window after revocation.            |

---

## 3. Narrative Interpretation

### 3.1 Risks

- **R1 – Performance under peak load**  
  Load-balancing and replication are in place, but without careful capacity planning, autoscaling, and DB tuning, the system may fail to meet P2 during exam peaks.

- **R3 – Data privacy enforcement**  
  Authorization must be implemented in a single, consistent place (ConversationService or API layer). If adapters perform their own checks inconsistently, bugs can undermine S1.

- **R4 – Integration modifiability**  
  If current design bypasses adapters and accesses external systems directly, integrating a new analytics system will require refactoring, increasing cost and schedule risk.

### 3.2 Non-Risks

- **R2 – Node failure handling**  
  Because app nodes are stateless and behind a load balancer, losing a node only reduces capacity slightly. Health checks and replication support A1.

- **R5 – Updating the LLM**  
  Since the AI model is wrapped by a dedicated adapter and selected via configuration, switching to a new model is low-risk from both code and availability perspectives.

### 3.3 Sensitivity Points

- **R6 – End-to-end latency**  
  Response time depends strongly on:
  - Cache hit rate and TTL settings.
  - Whether calls to LMS and Calendar are parallelized.
  - DB replica performance.  
    These are parameters to be tuned using monitoring data.

### 3.4 Tradeoff Points

- **R7 – Retry vs responsiveness**  
  A more aggressive retry policy improves robustness when LMS is flaky but can make AIDAP responses slower or contribute to thundering-herd effects after outages.

- **R8 – Security vs overhead**  
  Very strict token validation and logging improve security but add CPU and latency overhead. Some caching of token checks is acceptable if aligned with institutional security policy.

---

## 4. Conclusion

The ATAM analysis shows that:

- The design is strong in fail-over and in supporting LLM evolution.
- The main open risks relate to **scalability**, **authorization correctness**, and **integration modifiability**.
- Several sensitivity and tradeoff points (caching, retries, token validation) must be tuned based on production metrics.

These findings can be used to prioritize future implementation and testing tasks in Phase 4 (if the project continued beyond this course).
