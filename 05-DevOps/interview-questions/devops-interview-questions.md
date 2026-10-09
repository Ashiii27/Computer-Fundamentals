# DevOps — Top 30 Interview Questions (with Answers)

> Concept questions + tool probes. For Docker/K8s, expect follow-ups like "have you actually run it?" — do the labs in the resources file.

---

### 1. What is DevOps? Is it a role, a tool, or a culture?
A **culture and practice set** that unifies development and operations through automation, small frequent releases, shared ownership, and measurement (CALMS: Culture, Automation, Lean, Measurement, Sharing). Tools implement it, but buying Jenkins doesn't make you DevOps — shortening and de-risking the path from commit to production does.

### 2. Explain the DevOps lifecycle.
Plan → Code → Build → Test → Release → Deploy → Operate → Monitor, looping continuously. Each stage maps to tooling: planning (Jira), code (Git), build/test (CI: Jenkins/GitHub Actions), release/deploy (CD: ArgoCD, Terraform), operate/monitor (Kubernetes, Prometheus/Grafana, ELK).

### 3. CI vs Continuous Delivery vs Continuous Deployment?
**CI**: every push triggers build + automated tests on the mainline (fast feedback). **Continuous Delivery**: everything after that is automated too — artifact staged and kept deployable — but a human approves the production release. **Continuous Deployment**: production releases are fully automatic for every passing change. The difference is only who says "go".

### 4. DevOps vs Agile?
Agile optimizes the development loop (iterative delivery, scrum/kanban) and traditionally ends at "dev complete". DevOps extends through deployment and operations — release automation, infrastructure as code, production monitoring — making "done" mean "running in prod". Most orgs practice both; they're complementary, not competing.

### 5. Monolith vs microservices — DevOps implications?
Monolith: one deployable, simple ops, single DB — but slow builds and whole-app deployments. Microservices: independently deployable services — smaller blast radius, team autonomy, per-service scaling; the cost is distributed-system complexity (networks, consistency, tracing), which is exactly why containers/K8s/CI-CD/observability became mandatory. DevOps practices enable microservices; without automation they collapse into chaos.

### 6. Git merge vs rebase?
Merge joins branches with a merge commit — history shows what actually happened. Rebase replays your commits onto the target branch — linear, readable history, but rewrites commit hashes, so **never rebase branches others have pulled**. Common flow: rebase your feature branch onto main locally, squash-merge the PR.

### 7. `git reset` vs `git revert`?
`reset` moves the branch pointer backward — `--soft` keeps changes staged, `--mixed` unstages, `--hard` discards them; it rewrites history, fine for private branches. `revert` creates a new commit that inverts an old one — history stays intact, the safe choice on shared branches (and the only option after push).

### 8. What belongs in a CI pipeline?
Lint/static analysis → build → unit tests → package a versioned artifact (never `latest`) → integration tests → publish to registry → deploy to staging. Principles: fail fast (unit tests < 10 min), build **once** and promote the same artifact to prod, ephemeral clean agents, secrets from a vault, PR checks blocking merge.

### 9. Declarative vs scripted Jenkins pipeline?
Declarative (`pipeline { agent any; stages { stage('Build') { steps { … } } } }`) — structured, opinionated, readable, most teams' default. Scripted (raw Groovy) — maximum flexibility, harder to read/test. Prefer declarative + shared libraries for reusable logic.

### 10. Virtual machine vs container — how do they differ under the hood?
VMs virtualize hardware via a hypervisor; each guest runs a full OS (GBs, minutes to boot, strong isolation). Containers are OS-level virtualization: processes sharing the **host kernel**, isolated by **namespaces** (what they see) and limited by **cgroups** (what they may consume), with a layered union filesystem — MBs, milliseconds to start. VMs isolate better across tenants; containers give density and speed.

### 11. Docker image vs container?
Image: immutable, layered filesystem template + metadata, stored in a registry. Container: a running (or stopped) process instantiated from an image — image layers (read-only, shared/cached between containers) + one writable layer on top. `docker run image → container`; `docker commit` can freeze a container back into an image (but you should rebuild from Dockerfile instead).

### 12. CMD vs ENTRYPOINT?
`ENTRYPOINT` is the container's fixed executable; `CMD` provides default arguments that are **overwritten** if you pass args to `docker run`. Together: `ENTRYPOINT ["/app/server"]` + `CMD ["--port", "8080"]` → `docker run img --port 9090` runs `/app/server --port 9090`. CMD alone = fully overridable default command.

### 13. COPY vs ADD in a Dockerfile?
COPY copies files/dirs, plain and predictable — the default choice. ADD additionally auto-extracts local tar archives and can fetch remote URLs — magic that surprises people; the convention is COPY unless you specifically need tar auto-extraction.

### 14. What are multi-stage builds and why use them?
A Dockerfile with multiple FROM stages: a fat "builder" stage with compilers/SDK, then a final stage that copies **only** the built artifact into a slim base (alpine/distroless). Result: images 10–100× smaller, fewer CVEs, no build tools in prod, and layer caching keeps CI fast.

### 15. How do containers isolate processes?
Namespaces give each container private views: `pid` (own process tree), `net` (own interfaces/ports), `mnt` (own filesystem), `uts`, `ipc`, `user`. cgroups cap CPU/memory/IO per container. Union filesystem layers are copy-on-write. It's lighter than VM isolation (shared kernel — that's why a kernel vuln affects all containers, and why hard trust boundaries still use VMs).

### 16. What is Kubernetes and why did it win?
A container **orchestrator**: declaratively schedules and manages container fleets — self-healing, scaling, rollouts, service discovery, config/secrets. It won because it's cloud-neutral (every major cloud offers managed K8s), extensible (CRDs/operators), and backed by the CNCF ecosystem. Alternative story:Nomad, ECS — but K8s is the default answer.

### 17. Explain Kubernetes architecture.
**Control plane**: API server (all communication), etcd (cluster state store), scheduler (assigns pods to nodes), controller-manager (reconciliation loops). **Each node**: kubelet (agent executing pod specs), kube-proxy (service networking rules), container runtime (containerd). Declarative core: you submit desired state via YAML; controllers continuously reconcile actual→desired — that's how dead pods get recreated.

### 18. Pod vs container? Deployment vs ReplicaSet vs pod?
Pod = smallest schedulable unit: one or more co-located containers sharing IP, volumes, lifecycle (normally one container). ReplicaSet maintains N replicas. **Deployment** manages ReplicaSets to give you declarative rollouts, revision history (`rollout undo`), and scaling — the normal way to run stateless apps. StatefulSets for stable identity/storage (databases), DaemonSets for per-node agents.

### 19. Explain Kubernetes Service types.
`ClusterIP` — internal virtual IP + DNS name (default). `NodePort` — opens a static port (30000–32767) on every node. `LoadBalancer` — provisions a cloud load balancer in front. `ExternalName` — DNS CNAME alias. Services give stable endpoints despite pod churn; label selectors decide membership. Ingress (or Gateway API) sits above for L7 host/path routing + TLS.

### 20. ConfigMap vs Secret? How are they used?
ConfigMap: non-sensitive configuration (env vars, config files) injected into pods. Secret: same mechanism but for sensitive data — base64-encoded (not encrypted by default!), so also enable etcd encryption, RBAC on secrets, and ideally external managers (Vault, cloud secret managers). Both mount as env vars or files and can be updated without rebuilding images.

### 21. What happens when a pod crashes? What is self-healing?
The kubelet's liveness probe fails (or the container exits) → kubelet restarts the container per backOff policy; the ReplicaSet notices a missing pod and creates a replacement. If the whole node dies, the control plane reschedules its pods elsewhere (after eviction timeouts). Readiness probes gate traffic; liveness probes heal hangs; startup probes protect slow boots.

### 22. What is Helm?
The package manager for Kubernetes: **charts** are templated manifests + `values.yaml` defaults; per-environment values files render releases (`helm install/upgrade`). Solves manifest duplication across environments, supports hooks and rollbacks. Alternatives: Kustomize (overlay patching, no templating).

### 23. What is Terraform state and why does it matter?
`terraform.tfstate` maps your code to real infrastructure IDs — Terraform needs it to compute plan diffs, detect drift, and destroy correctly. For teams: **remote state** (S3/GCS/Terraform Cloud) with **locking** so two applies can't corrupt it; state may contain secrets, so encrypt and restrict access. Losing state means Terraform can no longer manage what it created.

### 24. Terraform vs Ansible?
Terraform: **provisions** infrastructure (VPCs, clusters, VMs) — declarative HCL, stateful, dependency graph, plan/apply. Ansible: **configures** systems (packages, configs, deploys) — agentless over SSH, YAML playbooks, idempotent modules, no persistent state. Typical chain: Terraform builds, Ansible configures. Overlap exists (Ansible can call cloud APIs; Terraform has provisioners) — pick per layer.

### 25. What is idempotency and why is it holy in automation?
Running the same operation twice yields the same result as once. Idempotent playbooks/containers/pipelines make retries safe, enable convergence ("ensure nginx is running"), and prevent config drift. Anti-pattern: shell scripts that `apt install` and `echo >>` unconditionally — second run mutates or fails.

### 26. Blue-green vs canary deployment?
**Blue-green**: run two identical environments; deploy to idle one, test, then flip traffic atomically — instant rollback (flip back) but 2× cost and a big-bang exposure. **Canary**: shift a small % of traffic to the new version (1→10→50→100%), watching error rate/latency, auto-aborting on regression — safer statistically, needs traffic splitting (service mesh/ingress/LB) plus good observability. Internal low-risk tools: rolling is fine; customer-facing high-risk: canary.

### 27. Monitoring vs observability? Three pillars?
Monitoring answers predefined questions ("is the error rate up?") with dashboards and alerts. Observability is the property that lets you debug **novel** failures from the system's outputs: **metrics** (numbers over time), **logs** (events with context), **traces** (per-request journeys across services) — unified today by OpenTelemetry. You can monitor without being observable; you can't debug hard problems without observability.

### 28. Explain the Prometheus + Grafana stack.
Prometheus scrapes HTTP `/metrics` endpoints (pull model) on a schedule, stores time series, exposes **PromQL** (`rate(http_requests_total{status="500"}[5m])`); Alertmanager evaluates alert rules and routes notifications with dedup/silencing; Grafana provides dashboards. Instrumentation via client libraries and exporters (node_exporter, kube-state-metrics). Pushgateway exists for short-lived jobs.

### 29. SLI vs SLO vs SLA? What is an error budget?
SLI: the measured indicator (p99 latency, % successful requests). SLO: your internal reliability target on that SLI (99.9%). SLA: the contractual promise with penalties (always looser than the SLO). **Error budget** = 100% − SLO: the tolerable failure you may deliberately spend on risky releases and experiments; budget exhausted → freeze features and pay down reliability debt. It turns reliability into a negotiable engineering currency.

### 30. What are the DORA metrics?
1. **Deployment frequency** (how often you release), 2. **Lead time for changes** (commit → prod), 3. **Mean time to restore** (how fast you recover from incidents), 4. **Change failure rate** (% of deploys causing incidents). Elite: on-demand deploys, lead time < 1 day, MTTR < 1 hour, CFR < 15%. They measure the *outcome* of DevOps, not tool adoption.

---

## 💡 How to answer DevOps questions well
- Anchor in the **problem each tool solves**, then the tool ("we needed identical envs → containers").
- Admit experience honestly, then show conceptual depth + a lab you've run (Play with Docker/Killercoda).
- For scenario questions ("prod is down, pods crash-looping"), answer as a checklist: kubectl describe → logs → probes → resources → events; incident process first, fix second.
