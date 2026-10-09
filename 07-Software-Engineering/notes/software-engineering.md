# Software Engineering — Complete Revision Notes

> Covers: SE basics → Process models → Requirements → Design → UML → Testing → Maintenance → Project & quality → Cheat sheet

## Table of Contents
1. [Introduction](#1-introduction)
2. [Process Models (SDLC)](#2-process-models-sdlc)
3. [Agile, Scrum & Kanban](#3-agile-scrum--kanban)
4. [Requirements Engineering](#4-requirements-engineering)
5. [Software Design](#5-software-design)
6. [UML Diagrams](#6-uml-diagrams)
7. [Software Testing](#7-software-testing)
8. [Software Maintenance](#8-software-maintenance)
9. [Project Management & Quality](#9-project-management--quality)
10. [Cheat Sheet](#10-cheat-sheet)

---

## 1. Introduction

- **Software Engineering**: systematic, disciplined, quantifiable approach to developing, operating, and maintaining software (IEEE). Born from the 1968 "software crisis" — projects late, over budget, unreliable.
- **Software = program + documentation + operating procedures**. Engineering discipline ≠ hacking something that works once.
- Why it matters: software is long-lived and changed by many hands — process, reviews, and tests are what keep it alive.
- **SDLC phases** (every model is a way of arranging these): Requirement analysis → Design → Implementation/Coding → Testing → Deployment → Maintenance.

---

## 2. Process Models (SDLC)

### Waterfall
Linear phases, each completed before the next; sign-off gated.
Pros: simple, well-documented, good for stable/regulated requirements.
Cons: working software only at the end; change is expensive; customer sees nothing early. (Pure waterfall is rare today.)

### V-Model
Waterfall where each dev phase pairs a test phase: requirements↔acceptance, design↔integration, coding↔unit. Verification built in; still rigid.

### Incremental & Iterative
Build in slices (increment = add functionality; iteration = refine the whole). Early partial delivery, feedback each round; needs good architecture. (Agile is the modern descendant.)

### Spiral (Boehm)
Risk-driven cycles: **plan → risk analysis → engineer → evaluate**; each loop is a prototype of the next riskiest part. Good for large, risky projects; costly; needs risk-analysis expertise.

### RAD / Prototyping
Rapid component-based dev with heavy user feedback (RAD); throwaway prototype to nail unclear requirements, or evolutionary prototype grown into the product.

| Model | Requirements | Risk handling | Customer involvement | Best when |
|---|---|---|---|---|
| Waterfall | Frozen upfront | Low | Start & end | Stable, well-known, regulated |
| V-Model | Frozen | Via test pairing | Limited | Safety-critical, test-heavy |
| Incremental | Clearable per slice | Medium | Each increment | Phased delivery possible |
| Spiral | Evolving | **Explicit, high** | Each cycle | Large, high-risk, novel |
| Agile | Evolving | Continuous | **Continuous** | Fast-changing products |

---

## 3. Agile, Scrum & Kanban

### Agile (the manifesto, 2001)
**Values**: individuals & interactions > processes/tools; working software > comprehensive documentation; customer collaboration > contract negotiation; responding to change > following a plan. (The left items are valued, the right aren't worthless.) Principles: deliver frequently (weeks), welcome change, business+dev work daily, sustainable pace, technical excellence, simplicity.

### Scrum
- **Roles**: Product Owner (what & priority — backlog), Scrum Master (process servant-leader, removes blockers), Development Team (cross-functional, self-organizing).
- **Events**: Sprint (1–4 weeks, fixed), Sprint Planning, Daily Scrum (15 min, 3 questions), Sprint Review (demo increment), Sprint Retrospective (process improvement).
- **Artifacts**: Product Backlog (prioritized by value), Sprint Backlog, Increment (potentially shippable, meets Definition of Done).
- Core ideas: empirical process control — transparency, inspection, adaptation.

### Kanban
Visualize flow on a board (To do → Doing → Done) with **WIP limits**, pull system, measure lead/cycle time, continuous flow (no fixed sprints).

| Scrum | Kanban |
|---|---|
| Fixed-length sprints | Continuous flow |
| Roles prescribed | No prescribed roles |
| Estimate & commit per sprint | WIP limits + metrics |
| Best for planned feature teams | Best for ops/support/flow work |

Extreme Programming (XP): engineering practices under agile — **TDD, pair programming, CI, refactoring, collective code ownership**.

---

## 4. Requirements Engineering

- **Functional requirements** — what the system *does*: "user can reset password via email OTP".
- **Non-functional requirements** — qualities/constraints: performance (p95 < 200ms), scalability, availability (99.9%), security, usability, maintainability, compliance. (Often forgotten, often decisive.)
- **Process**: elicitation (interviews, workshops, observation) → analysis (conflicts, feasibility) → specification (SRS) → validation (reviews, prototypes).
- **SRS** characteristics: correct, unambiguous, complete, consistent, verifiable, traceable, modifiable. Written for customers + devs + testers.
- Requirements traceability matrix: requirement ↔ design ↔ code ↔ test linkage.

---

## 5. Software Design

- **Architecture** (macro: modules & interactions) vs **detailed design** (classes, algorithms, data structures).
- Goals: high **cohesion**, low **coupling**, information hiding (Parnas), abstraction, separation of concerns.
- **Coupling** (inter-module dependence — minimize): data < stamp < control < common < content coupling (worst).
- **Cohesion** (intra-module focus — maximize): functional (best) > sequential > communicational > procedural > temporal > logical > coincidental (worst).
- Common architectures: layered, client–server, **MVC/MVVM**, microservices (independently deployable, own data) vs monolith, event-driven, pipe-and-filter.
- Design heuristics: program to interfaces, DRY, YAGNI, design for change; effective modularity = right-sized modules (neither god-modules nor confetti).

---

## 6. UML Diagrams

Structural: **class diagram** (the classic: classes, attributes, methods, associations/arrows), component, deployment, object, package.
Behavioral: **use case** (actors + goals — requirement-level picture), **sequence** (object interactions over time — great for APIs), **activity** (workflow/flowchart), **state** (state machine of one object), communication.

Interview-relevant shorthand:
- Use case = what the system offers actors (include/extend relationships).
- Sequence = ordered messages between lifelines (know how to draw a login flow).
- Class = boxes with name/attributes/methods; visibility +/−/#; arrows: association → aggregation ◇ → composition ◆ → inheritance △ → implements (dashed △).

---

## 7. Software Testing

### Levels (in order)
| Level | Who | What |
|---|---|---|
| **Unit** | Devs | Smallest pieces (functions/classes), mocked dependencies |
| **Integration** | Devs/QA | Interfaces between units — **big-bang** (all at once) vs **top-down** (stubs) vs **bottom-up** (drivers) vs sandwich |
| **System** | QA | Whole integrated system vs SRS |
| **Acceptance** | Customer/UAT | Alpha (in-house, by test teams) & **Beta** (real users, real environment) |

### Types
- **Functional** vs **non-functional** (performance, load, stress, security, usability, compatibility).
- **Black box** (spec-based, no internals): **equivalence partitioning** (one value per class), **boundary value analysis** (bugs live at edges — test 0, 1, max, max+1), decision tables, state transition.
- **White box** (code-aware): statement/branch/path coverage, cyclomatic complexity (V(G) = E − N + 2P = independent paths).
- **Grey box**: partial knowledge (integration/API testing).

### The confusing pairs (exam + interview favorites)
| Pair | Difference |
|---|---|
| **Smoke vs Sanity** | Smoke: broad & shallow "is the build even testable?" after a new build. Sanity: narrow & deep "did that specific fix work?" before deeper testing. |
| **Retesting vs Regression** | Retesting: re-run the *failed* cases after a fix. Regression: re-run *passing* areas to catch side effects of the change. |
| **Verification vs Validation** | Verification: "are we building the product *right*" (reviews, walkthroughs, inspections — no code execution). Validation: "are we building the *right product*" (actual testing against user needs). |
| **Test stub vs driver** | Stub = called-by replacement (top-down), driver = calls-the-module harness (bottom-up). |
| **Static vs dynamic** | Reviews/inspections/linters vs actually executing tests. |

- **TDD**: write the failing test → minimal code to pass → refactor (red-green-refactor). **BDD**: Given-When-Then scenarios shared with business (Cucumber).
- **Test pyramid**: many unit, some integration, few E2E — fast feedback at the base.
- **Defect life cycle**: New → Assigned → Fixed → Retested → Verified/Closed (or Reopened; also Duplicate/Deferred/Rejected). Severity (impact) vs priority (urgency) can differ (crash on a rarely used legacy page = high severity, low priority).

---

## 8. Software Maintenance

~60–70% of lifetime cost happens after delivery. Four types:
| Type | Trigger | Example |
|---|---|---|
| **Corrective** | Bugs | Fix a crash on checkout |
| **Adaptive** | Environment change | New OS/browser/tax law |
| **Perfective** | User requests | Add filters, improve UX |
| **Preventive** | Future-proofing | Refactor, upgrade deps, pay tech debt |

Related: reverse engineering (code → design), re-engineering (understand + rebuild), refactoring (behavior-preserving cleanup), technical debt (shortcuts compounding like debt).

---

## 9. Project Management & Quality

- **Estimation**: LOC (naive), **Function Points** (language-neutral complexity units), COCOMO (effort = a·(KLOC)^b; basic/intermediate/detailed), story points + velocity (agile).
- **Risk management**: identify → analyze (prob × impact) → plan (avoid/mitigate/transfer/accept) → monitor. Top risks: scope creep, staff turnover, underestimated complexity.
- **CMMI maturity levels**: 1 Initial (chaos) → 2 Managed (project-level discipline) → 3 Defined (org-wide process) → 4 Quantitatively Managed (measured) → 5 Optimizing (continuous improvement).
- **Team structures**: chief-programmer, egoless/democratic, hierarchical. **Brooks' Law**: adding people to a late project makes it later (onboarding + communication paths grow as n(n−1)/2).
- Quality assurance (process-oriented, preventive — audits, standards) vs quality control (product-oriented, detective — testing, reviews).
- Version control discipline: short-lived branches, small PRs, mandatory review, CI on every push (see DevOps notes).

---

## 10. Cheat Sheet

| Concept | One-liner |
|---|---|
| SDLC phases | Requirements → Design → Code → Test → Deploy → Maintain |
| Waterfall | Frozen requirements, phase gates, late feedback |
| Spiral | Risk-driven loops (plan → risk → build → evaluate) |
| Agile values | Working software, individuals, collaboration, responding to change |
| Scrum trio | PO (what) + SM (how/process) + Team (build) |
| Sprint events | Planning → Daily → Review (demo) → Retro (process) |
| Kanban core | Visualize flow + WIP limits + pull |
| Functional vs non-functional | Behavior vs qualities (perf/security/usability) |
| Coupling vs cohesion | Between modules (minimize) vs within a module (maximize) |
| Verification vs validation | Building it right (reviews) vs building the right thing (tests) |
| Alpha vs beta | In-house users vs real users, real environment |
| Smoke vs sanity | Broad-shallow build check vs narrow-deep fix check |
| Retesting vs regression | Failed cases again vs untouched areas after change |
| EP / BVA | Test one per class / test the edges |
| Cyclomatic complexity | E − N + 2P = independent paths = min tests for branch coverage |
| Stub vs driver | Fake callee (top-down) vs fake caller (bottom-up) |
| Maintenance 4 types | Corrective, adaptive, perfective, preventive |
| CMMI | Initial → Managed → Defined → Quantitatively managed → Optimizing |
| Brooks' law | Adding people to a late project makes it later |
| Test pyramid | Many unit, some integration, few E2E |
