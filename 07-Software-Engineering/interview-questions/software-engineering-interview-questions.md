# Software Engineering — Top 25 Interview Questions (with Answers)

> Theory-heavy subject — answers below are sized for spoken interviews. Expand with examples from your own projects for extra credit.

---

### 1. What is software engineering and why is it needed?
The disciplined, systematic approach to developing, operating, and maintaining software. It exists because ad-hoc coding failed at scale — the "software crisis" (late, over-budget, unreliable systems). Process, design, and testing let teams build systems far larger than any individual's mental model, and keep them maintainable for years.

### 2. What are the phases of the SDLC?
Requirement analysis → Design (architecture + detailed) → Implementation → Testing → Deployment → Maintenance. Each phase produces artifacts (SRS, design docs, code, test reports) that feed the next; different process models just arrange/iterate these phases differently.

### 3. Compare waterfall, spiral, and agile.
Waterfall: linear, frozen requirements, single delivery at the end — predictable but change-hostile. Spiral: repeated risk-driven cycles (plan → risk analysis → engineer → evaluate) — handles risk and evolving requirements, at high process cost. Agile: short iterations delivering working software with continuous customer feedback — best for changing requirements, needs involved customers and disciplined engineering. Choose by requirement stability, risk, and customer availability.

### 4. What is the V-model?
An extension of waterfall where every development phase has a paired test level: requirements ↔ acceptance testing, system design ↔ system testing, module design ↔ integration testing, coding ↔ unit testing. Verification is planned from day one instead of bolted on; still assumes stable requirements.

### 5. Explain the Agile Manifesto values.
(1) Individuals and interactions over processes and tools; (2) working software over comprehensive documentation; (3) customer collaboration over contract negotiation; (4) responding to change over following a plan. The items on the right still have value — agile just values the left more. Supporting principles include frequent delivery, daily collaboration, sustainable pace, and technical excellence.

### 6. Explain Scrum — roles, events, artifacts.
Roles: Product Owner (owns and prioritizes the backlog), Scrum Master (guards the process, removes impediments), Developers (cross-functional, self-organizing). Events: Sprint (1–4 week fixed cadence), Sprint Planning, Daily Standup (15-min sync), Sprint Review (demo the increment), Retrospective (improve the process). Artifacts: Product Backlog, Sprint Backlog, Increment (meets Definition of Done). It's empirical: transparency → inspection → adaptation.

### 7. Scrum vs Kanban?
Scrum: fixed sprints, committed sprint goals, prescribed roles, estimation and velocity. Kanban: continuous flow, no mandated roles or cadence, WIP limits to expose bottlenecks, metrics = lead/cycle time. Feature teams building products often fit Scrum; ops/support/interrupt-driven work fits Kanban; many teams hybridize (Scrumban).

### 8. Functional vs non-functional requirements?
Functional: what the system does — features and behaviors ("reset password via OTP"). Non-functional: qualities and constraints — performance, scalability, availability, security, usability, compliance ("p95 < 200 ms at 1k RPS", "99.9% uptime"). NFRs are easy to skip and expensive to retrofit; they drive most architecture decisions.

### 9. What makes a good SRS?
Correct, unambiguous, complete, consistent, ranked for importance, verifiable (each requirement testable), traceable, and modifiable. It's the contract among customer, developers, and testers — ambiguity here becomes rework later.

### 10. What is coupling and cohesion? What should they be?
Cohesion: how strongly a module's internals belong to one purpose — maximize it (functional cohesion is ideal). Coupling: how dependent modules are on each other's internals — minimize it (data coupling is best; avoid common/content coupling). High cohesion + low coupling ⇒ modules you can understand, test, and change in isolation.

### 11. Explain black-box testing techniques.
Equivalence partitioning: split inputs into classes treated alike, test one per class (age 18–60 → test 30, not all 43 values). Boundary value analysis: defects cluster at edges — test 17/18 and 60/61. Also: decision tables (rule combinations), state-transition testing, error guessing. Black-box = testing against the spec without seeing code.

### 12. White-box testing and code coverage?
Testing with knowledge of the code: statement coverage (every line), branch coverage (every decision's true/false), path coverage (every route — infeasible in general). Cyclomatic complexity V(G) = E − N + 2P gives the number of independent paths = lower bound on branch-complete tests; also a maintainability smell when > 10ish.

### 13. Verification vs validation?
Verification: "are we building the product right?" — conformance to spec via reviews, inspections, walkthroughs, static analysis (no execution needed). Validation: "are we building the right product?" — does it meet actual user needs, via real testing/UAT. A system can be perfectly verified against a wrong spec — that's why you need both.

### 14. Smoke vs sanity testing?
Smoke: after a new build — broad, shallow checks ("does it even launch and serve the main flow?") to decide if deeper testing is worth it. Sanity: narrow, focused checks that a specific fix/feature works before investing in regression. Smoke = build acceptance; sanity = change acceptance.

### 15. Retesting vs regression testing?
Retesting: re-run the specific test cases that failed, to confirm the fix. Regression: re-run previously passing tests around the change, to catch unintended side effects. Every fix needs retesting; every release needs regression (automated, ideally).

### 16. Alpha vs beta testing?
Alpha: acceptance testing by internal/test teams at the developer's site, controlled environment. Beta: limited release to real users in their own environments — catches environment-specific and usability issues. Alpha is "is it ready to show?"; beta is "does it survive the wild?"

### 17. Explain the testing levels.
Unit (smallest pieces, mocked deps, written by devs) → Integration (units together; big-bang, top-down with stubs, bottom-up with drivers) → System (whole product vs SRS: functional + performance/security) → Acceptance (user/UAT; alpha & beta). Order matters: fail fast and cheap at the bottom of the test pyramid.

### 18. What is TDD? BDD?
TDD: write a failing test first, write the minimal code to pass it, refactor — red/green/refactor; produces tight, testable designs and a safety net. BDD: same loop but scenarios in business language (Given-When-Then, e.g., Cucumber) so product and QA share the specs. Both shift defect detection left, where fixes are cheapest.

### 19. Explain the defect (bug) life cycle.
New → Assigned → In progress → Fixed → Retested → Verified → Closed; branches: Reopened (fix failed), Rejected (not a bug), Duplicate, Deferred (won't fix now). Also know **severity** (technical impact) vs **priority** (business urgency): a data-corruption bug on a rarely used screen = high severity, low priority.

### 20. What are the four types of maintenance?
Corrective (fix defects), Adaptive (adjust to environment changes — new OS/APIs/laws), Perfective (new features/improvements per user requests — the largest share), Preventive (refactoring, dependency upgrades to prevent future problems). Real-world note: most cost is post-delivery — maintenance *is* software engineering.

### 21. What is technical debt?
The future cost implied by quick-and-dirty choices — like financial debt, it accrues "interest" (every later change is slower and riskier) until repaid via refactoring. Not all debt is bad (deliberate, documented shortcuts to hit a deadline are a business decision); unmanaged invisible debt is what kills velocity.

### 22. What is Brooks' Law?
"Adding manpower to a late software project makes it later." New people need training and split the team's attention, while communication paths grow ~n²/2 — short-term slowdown before any speedup. Mitigation: onboard in small batches, protect modular architecture, add people only to parallelizable work.

### 23. Explain the CMMI levels.
(1) Initial — ad hoc, hero-dependent; (2) Managed — project-level planning & tracking; (3) Defined — organization-wide standardized process; (4) Quantitatively Managed — statistical process control with metrics; (5) Optimizing — continuous improvement feeds back into the process. It's a maturity ladder for org process discipline, not a guarantee of product quality by itself.

### 24. Waterfall vs Agile — when would you still pick waterfall?
Pick waterfall-ish flows when requirements are genuinely fixed and verifiable (regulatory/compliance, safety-critical embedded systems, fixed-scope contracts), the customer can't engage continuously, and change is contractually expensive. Everywhere else, feedback beats prediction — agile. Most real orgs run a pragmatic hybrid.

### 25. What makes code reviews and CI valuable, process-wise?
Reviews spread knowledge, catch defects when they're cheapest (design/logic stage — studies show review beats testing for some defect classes), and enforce standards. CI verifies every integration instantly so "merge conflicts" and "integration hell" shrink from weeks to minutes. Together they're the mechanism behind agile's "working software" promise (and are formalized in DevOps pipelines).

---

## 💡 How to answer SE theory questions
- Define → compare (table if ≥ 2 items) → one concrete example → when-to-use guidance.
- Expect "which would you choose for X project?" — always answer with trade-offs, not absolutes.
- Map concepts to your own project experience ("we did UAT via a beta cohort…") — that's what separates 7/10 from 10/10 answers.
