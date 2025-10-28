# AIDAP – AI-Powered Digital Assistant Platform

## Table of contents

- Overview
- Deliverables & schedule
- Repository layout (Deliverable 1)
- Business case
- How to contribute
- Next steps

## Overview

AI‑Powered Digital Assistant Platform (AIDAP) is a university-focused conversational system that provides students, lecturers, and administrators with centralized access to institutional information (course schedules, deadlines, announcements, analytics). AIDAP integrates with existing systems (LMS, registration, calendars, email) and uses AI models for NLU and contextual response generation. The design follows Attribute‑Driven Design (ADD) to prioritize quality attributes and architectural drivers.

## Deliverables & schedule

- Deliverable 1 — Requirements Analysis (Due: Oct 28, 2025)
	- Use cases, quality attributes, system constraints, architectural concerns, and business case.
- Deliverable 2 — ADD Iterations 1 & 2 (Due: Nov 16, 2025)
	- Candidate architectures, chosen reference architecture, component & deployment diagrams, interface specifications.
- Deliverable 3 — Design of a Use Case (Due: Dec 6, 2025)
	- Implement a use case, map code to architecture, and perform ATAM-style scenario analysis.

Course: SOFE 3650 – Software Design & Architectures (Fall 2025)
Instructor: Dr. Hani Sami

## Repository layout (Deliverable 1)

- `README.md` — this file (project overview, business case, links, next steps).
- `Use Cases.md` — actor list and primary use cases with flows and traceability.
- `Quality Attributes.md` — prioritized attributes, measurable scenarios, and tactics.
- `Constraints.md` — system constraints and implementation constraints.
- `Concerns.md` — architectural concerns and trade-offs to drive ADD.
- `ADD Iteration 1/` `ADD Iteration 2/` `ADD Iteration 3/` — iteration artifacts (kept for later ADD work).
- `diagrams/` — place UML (use-case, component, deployment) and images here (add files as PNG/SVG/PlantUML).

## Business case (brief)

- Problem: Institutional information is fragmented across multiple systems which increases friction for students and staff and delays important actions.
- Proposed solution: A conversational assistant (AIDAP) that consolidates access to schedule, announcements, analytics, and personal data; supports text and voice; and integrates with institutional systems.
- Key benefits:
	- Better student engagement and timely awareness of deadlines.
	- Reduced administrative support load for routine queries.
	- Centralized analytics for lecturers and admins to monitor engagement and performance.
- Primary risks:
	- Data privacy and compliance with institutional policies.
	- Integration complexity with legacy systems and varying APIs.
	- Cost vs latency trade-offs for AI model selection.

## How to contribute

- Use GitHub Issues to propose tasks; link issues to KanBan project board for tracking.
- Branching: create a branch per feature/issue and open a PR with linked issue(s).
- Add UML diagrams and other artifacts to `/diagrams` and reference them from ADD iteration documents.
- Keep documentation and design artifacts updated in the corresponding `ADD Iteration` folder.

## Next steps (recommended)

1. Add UML artifacts to `/diagrams`: use-case diagram and a deployment diagram for the chosen reference architecture (I can add PlantUML sources if you want).
2. Finalize ADD Iteration 1: component diagram, interface contracts for connectors, mapping of tactics to quality attributes.
3. Prepare a branch + PR for instructor review when ready.

---

If you want, I can now:

- add a `diagrams/` folder and drop in PlantUML sources for a use-case and deployment diagram, or
- go ahead and draft ADD Iteration 1 artifacts (components, interfaces, deployment) in `ADD Iteration 1/`.
