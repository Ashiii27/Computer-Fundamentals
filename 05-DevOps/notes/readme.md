# DevOps — Complete Revision Notes

> Covers: DevOps culture → Linux & Git → CI/CD → Docker → Kubernetes → IaC (Terraform/Ansible) → Monitoring & Observability → Deployment strategies → DevSecOps & DORA

## Table of Contents
1. [What is DevOps?](#1-what-is-devops)
2. [Linux & Scripting Essentials](#2-linux--scripting-essentials)
3. [Git & Version Control](#3-git--version-control)
4. [CI/CD](#4-cicd)
5. [Docker & Containers](#5-docker--containers)
6. [Kubernetes](#6-kubernetes)
7. [Infrastructure as Code (Terraform & Ansible)](#7-infrastructure-as-code-terraform--ansible)
8. [Monitoring & Observability](#8-monitoring--observability)
9. [Deployment Strategies](#9-deployment-strategies)
10. [DevSecOps, GitOps & DORA Metrics](#10-devsecops-gitops--dora-metrics)
11. [Cheat Sheet](#11-cheat-sheet)

---

## 1. What is DevOps?

**DevOps** = a culture + set of practices + tooling that merges **Development** (build) and **Operations** (run) so software can be delivered **frequently, reliably, and safely**.

- The old problem: devs want change, ops want stability → silos, slow releases, "it works on my machine".
- DevOps answer: automate everything, own outcomes together, ship small and often.
- **CALMS**: **C**ulture, **A**utomation, **L**ean (small batches, flow), **M**easurement, **S**haring.
- **DevOps vs Agile**: Agile optimizes *development* iteration (scrum, sprints); DevOps extends agility through *deployment and operations* — Agile ends at "done", DevOps makes "done = running in production". They complement each other.
- Lifecycle (infinite loop): **Plan → Code → Build → Test → Release → Deploy → Operate → Monitor → (back)**.
- Related roles: **SRE** (Google's engineering take on ops — SLOs, error budgets, toil reduction), **Platform engineering** (internal developer platforms).

---

## 2. Linux & Scripting Essentials

Why Linux: ~all servers/containers run it; DevOps lives in the terminal.

- Filesystem: `/etc` (configs), `/var/log` (logs), `/home`, `/proc`, everything-is-a-file.
- Daily commands:
  - Files: `ls -la`, `cd`, `cp/mv/rm`, `find . -name "*.log"`, `tree`
  - Text: `cat`, `less`, `head/tail -f file.log`, `grep -r`, `awk`, `sed`, `cut`, `sort | uniq -c | sort -nr`
  - System: `ps aux`, `top/htop`, `kill -9`, `df -h`, `du -sh *`, `free -h`, `uptime`, `vmstat`, `lsof -i :8080`
  - Network: `curl`, `wget`, `ping`, `traceroute`, `ss -tulpn`, `dig`, `ssh user@host`, `scp`
  - Perms: `chmod 755`, `chown user:group`, `sudo`
  - Packages/services: `apt/yum`, `systemctl status/restart nginx`, `journalctl -u nginx`
- **Shell scripting**: shebang `#!/bin/bash`, variables, `if/for/while`, exit codes (`$?`), pipes & redirection (`|`, `>`, `2>&1`), crontab scheduling (`crontab -e`, `* * * * *`).
- SSH keys: `ssh-keygen` → public key to server's `~/.ssh/authorized_keys`; agents & config files.

---

## 3. Git & Version Control

- **VCS types**: local → centralized (SVN) → **distributed** (Git — every clone is a full repo).
- Core model: commits are **snapshots** (not diffs) linked in a DAG; branches are just movable pointers; HEAD points to where you are.
- Daily flow: `git status`, `add`, `commit -m`, `push`, `pull --rebase`, `log --oneline --graph`.
- **Merge vs rebase**:
  | merge | rebase |
  |---|---|
  | Creates a merge commit; preserves true history | Replays your commits on top of target — linear history |
  | Safe for shared branches | **Never rebase published/shared branches** (rewrites history) |
- `git reset --soft/--mixed/--hard` (undo commits, keep/mix/discard changes) vs `git revert` (new commit undoing an old one — safe on shared branches).
- **PR/MR flow**: branch → commit → push → PR → review/approvals → CI checks → merge (squash for tidy history) → deploy.
- Branching strategies: **Git Flow** (develop/release/hotfix branches — heavy), **GitHub Flow** (branch from main, PR, deploy — simple, continuous delivery), **Trunk-based development** (tiny short-lived branches straight off main — needs strong CI + feature flags).
- Hooks (`pre-commit`, `pre-push`) gate bad commits; `.gitignore`; tags for releases.

---

## 4. CI/CD

- **Continuous Integration (CI)**: every push triggers automated **build + tests** on a shared branch — catch breakage within minutes (GitHub Actions, Jenkins, GitLab CI, CircleCI).
- **Continuous Delivery**: every change is *kept deployable* — artifact built, tested, staged; a **human** clicks "deploy".
- **Continuous Deployment**: every passing change **auto-deploys** to production. Delivery→deployment is purely a business-risk decision.

### Typical pipeline
```
commit → lint/static analysis → build → unit tests → package artifact (+version)
      → integration/e2e tests → push to registry → deploy staging → (approval) → deploy prod
```
- Principles: fast feedback (unit < 10 min), fail loudly, build **once** and promote the same artifact through environments, keep secrets in vaults not pipelines, ephemeral clean build agents.
- **Jenkins**: self-hosted automation server; **declarative pipeline** (`pipeline { stages { … } }` in Jenkinsfile) vs scripted (Groovy); agents/slaves distribute jobs. GitHub Actions: YAML workflows, marketplace actions, runners; jobs = steps in containers/VMs; matrix builds.
- **Artifacts & registries**: versioned images (`app:1.4.2`, plus git SHA tags), Docker Hub/ECR/GHCR, Artifactory/Nexus for binaries. **Never** `latest` in prod manifests.

---

## 5. Docker & Containers

### VM vs Container
| VM | Container |
|---|---|
| Hypervisor virtualizes **hardware**; full guest OS per VM | OS-level virtualization — containers share the **host kernel** |
| GBs, minutes to boot | MBs, milliseconds to start |
| Strong isolation | Process-level isolation (namespaces + cgroups) |
| VMware, VirtualBox, KVM | Docker, containerd, Podman |

**Isolation primitives**: **namespaces** (what the process can *see*: pid, net, mnt, uts, ipc, user) + **cgroups** (what it can *use*: CPU, memory, IO limits) + union filesystem (images as stacked layers).

### Docker architecture
Client (`docker build/run/push`) → Docker daemon (containerd) → registries (Docker Hub, ECR). **Image** = immutable template of read-only layers; **container** = running instance = image layers + a writable layer.

### Dockerfile essentials
```dockerfile
FROM node:20-alpine # small base (alpine/slim/distroless)
WORKDIR /app
COPY package*.json ./
RUN npm ci # dependency layer cached unless lockfile changes
COPY . .
RUN npm run build
ENV NODE_ENV=production
EXPOSE 3000
USER node # don't run as root
CMD ["node", "dist/main.js"]
```
| Instruction | Meaning / gotcha |
|---|---|
| `CMD` | Default command — **overridable** by `docker run` args |
| `ENTRYPOINT` | The fixed executable — run args get **appended**; pair with CMD for default flags |
| `COPY` vs `ADD` | COPY = plain copy (prefer). ADD also untars URLs/tars — avoid the magic |
| `RUN` | Executes at **build** time, creates a layer |
| `EXPOSE` | Documentation only — doesn't publish ports (`-p` does) |
| `ARG` vs `ENV` | Build-time vs runtime variable |

**Layer caching**: order layers least→most volatile (deps before source) → rebuilds only re-do what changed.
**Multi-stage builds**: compile in a fat builder stage, copy only the artifact into a slim runtime image → smaller images, fewer vulnerabilities.
**Compose** (`docker-compose.yml`): define multi-container apps (app + db + cache) with networks/volumes; `docker compose up`.
**Best practices**: pin versions, `.dockerignore`, one process per container, healthchecks, scan images (Trivy), don't bake secrets into images.

---

## 6. Kubernetes

**Why orchestrate?** At scale you need self-healing, scaling, rollouts/rollbacks, service discovery, secrets — K8s is the declarative control plane for container fleets.

### Architecture
| Control plane (master) | Worker node |
|---|---|
| **API server** — front door; everything talks to it | **kubelet** — node agent; runs pods per API server |
| **etcd** — the cluster's key-value store (all state) | **kube-proxy** — service networking/iptables-IPVS rules |
| **Scheduler** — assigns pods to nodes (resources, affinity, taints) | **Container runtime** — containerd/CRI-O |
| **Controller manager** — reconciliation loops (desired vs actual state) | |

Declarative model: you write **manifests** (YAML) describing desired state; controllers continuously reconcile reality toward it — that's why pods get recreated when they die (self-healing).

### Core objects
| Object | Purpose |
|---|---|
| **Pod** | Smallest deployable unit — 1+ co-located containers sharing IP/volumes (usually 1) |
| **ReplicaSet** | Keeps N identical pods alive |
| **Deployment** | Manages ReplicaSets → declarative rollouts/rollbacks, scaling |
| **StatefulSet** | Stable names + storage ordering (databases, brokers) |
| **DaemonSet** | One pod per node (log collectors, node agents) |
| **Job / CronJob** | Run-to-completion / scheduled tasks |
| **Service** | Stable virtual IP + DNS for a set of pods |
| **Ingress** | L7 HTTP routing/TLS from outside to services |
| **ConfigMap / Secret** | Config & sensitive data injected as env/files |
| **Namespace** | Virtual cluster partition (quotas, RBAC scoping) |
| **PV / PVC / StorageClass** | Persistent volume provisioning & claims |

**Service types**: `ClusterIP` (internal), `NodePort` (node-level port, 30000-32767), `LoadBalancer` (cloud LB), `ExternalName` (DNS alias).
**kubectl daily**: `get/describe/logs/exec -it`, `apply -f`, `rollout status/history/undo`, `scale`, `port-forward`, `top pods`.
**Helm**: package manager for K8s — charts = templated manifests; values per environment.
**Ecosystem landmarks**: cert-manager, Prometheus operator, ArgoCD/Flux (GitOps), service meshes (Istio/Linkerd — mTLS, traffic splitting).

---

## 7. Infrastructure as Code (Terraform & Ansible)

**IaC** = manage infra (networks, VMs, clusters) as versioned, reviewable code — reproducible, reviewable, disposable. Declarative (describe end state) beats imperative (click/scripts) for drift resistance.

### Terraform
- **Providers** plug into clouds (AWS/Azure/GCP/k8s); **HCL** resources; `terraform plan` (dry-run diff!) → `apply`.
- **State** (`terraform.tfstate`) is Terraform's mapping of code ↔ real resources: enables drift detection, dependency graph, `plan` diffs. **Remote state** (S3+DynamoDB lock / Terraform Cloud) for teams — locking prevents concurrent corrupting runs. State is sensitive (contains secrets!) — encrypt it.
- **Modules** package reusable infra; workspaces/branches per env; `import` adopts existing infra.

### Ansible (configuration management)
- Agentless (SSH), YAML **playbooks** of **roles/tasks**, Jinja2 templating, **idempotent modules** (run twice, same result).
- Terraform vs Ansible: Terraform *provisions* infrastructure (declarative, stateful, cloud resources); Ansible *configures* machines/apps (procedural-ish tasks, no permanent state). They're commonly chained: TF creates VMs → Ansible configures them.
- Others: Pulumi (real languages), CloudFormation (AWS-native), Packer (images), config mgmt elders: Puppet/Chef/Salt.
- **Configuration drift**: reality diverging from code — detected by plan/drift tools; fixed by re-apply, prevented by GitOps.

---

## 8. Monitoring & Observability

- **Monitoring** = watching predefined metrics/alerts ("is it broken?"). **Observability** = ability to interrogate arbitrary state from outputs ("*why* is it broken?") — built on three pillars:
  1. **Metrics** — numeric time series (CPU, latency p50/p95/p99, RPS, error rate) — Prometheus.
  2. **Logs** — discrete events with context — ELK/EFK (Elasticsearch-Logstash/Kibana or Fluentd), Loki.
  3. **Traces** — request journey across services (spans) — Jaeger, Tempo, OpenTelemetry (the vendor-neutral standard).

### Prometheus + Grafana (the default stack)
- Prometheus **pulls** metrics from HTTP `/metrics` endpoints (exporters: node_exporter, kube-state-metrics; pushgateway for short jobs).
- **PromQL** queries (`rate(http_errors[5m])`), **Alertmanager** routes alerts (PagerDuty/Slack) with grouping/silencing. Grafana visualizes dashboards.
- Alert hygiene: alert on **symptoms users feel** (latency, errors) not every CPU blip; every page must be actionable.

### SLO vocabulary
- **SLI** — the measured indicator (p99 latency, success rate). **SLO** — the target (99.9% success). **SLA** — the contract with penalties. **Error budget** = 1 − SLO: the allowed unreliability you may spend on releases/experiments; empty budget → freeze features, fix reliability.

---

## 9. Deployment Strategies

| Strategy | How | Rollback | Cost/risk |
|---|---|---|---|
| **Recreate** | Kill old, start new | Slow (full redeploy) | Simple; downtime window |
| **Rolling** | Replace pods batch-by-batch (K8s default) | Roll back batches | Capacity headroom needed; both versions live |
| **Blue-Green** | Two identical environments; switch traffic at once | Instant (flip back) | 2× infra; DB migrations need care |
| **Canary** | Route small % (1→10→50→100) to new version; watch metrics; auto-abort | Instant (shift %) | Needs traffic-splitting (mesh/LB/ingress) + observability |
| **A/B / shadow** | Split by user features / mirror traffic without responses | — | Experimentation; shadow needs care with side effects |

- **Feature flags**: decouple *deploy* from *release* — ship dark, enable per cohort, kill-switch instantly.
- **Rollback discipline**: DB migrations must be backward-compatible (expand→migrate→contract) so code rollbacks don't brick the schema.

---

## 10. DevSecOps, GitOps & DORA Metrics

- **DevSecOps**: shift security left — SAST (code scan), SCA (dependency scan), image scanning (Trivy), secrets scanning (gitleaks), policy as code (OPA), secrets in Vault/cloud secret managers (never in Git/images), least-privilege RBAC.
- **GitOps**: Git is the single source of truth for infra/deployments; an in-cluster agent (ArgoCD/Flux) continuously syncs cluster state to Git; PRs become deployments; drift is auto-corrected; audit trail = git history.
- **DORA metrics** (measure DevOps performance):
  1. **Deployment frequency** — how often to production
  2. **Lead time for changes** — commit → production
  3. **Mean time to restore (MTTR)** — recovery speed after incidents
  4. **Change failure rate** — % of deployments causing failures
  Elite teams: deploy daily+, lead time < 1 day, MTTR < 1 hour, CFR < 15%.

---

## 11. Cheat Sheet

| Concept | One-liner |
|---|---|
| CI vs CD vs CD | Integrate+test always → always-deployable artifact → auto-deploy to prod |
| Merge vs rebase | Preserve history vs linearize it; never rebase shared branches |
| reset vs revert | Move branch pointer (history rewrite) vs inverse commit (safe) |
| Image vs container | Immutable layered template vs running instance with writable layer |
| CMD vs ENTRYPOINT | Default command (overridable) vs fixed executable (args appended) |
| Namespaces vs cgroups | What a container *sees* vs what it *may consume* |
| Multi-stage build | Compile fat → ship slim; smaller, safer images |
| Pod vs deployment | Smallest unit vs rollout/scaling manager over ReplicaSets |
| Service types | ClusterIP (internal) / NodePort / LoadBalancer / ExternalName |
| ConfigMap vs Secret | Config vs sensitive data (base64, ideally encrypted/RBAC'd) |
| Terraform state | Code↔reality mapping enabling plan/drift; remote + locked for teams |
| Terraform vs Ansible | Provision infra (declarative, stateful) vs configure machines (agentless SSH) |
| Idempotency | Run twice = same result; core property of good automation |
| Blue-green vs canary | Big-bang switch between two envs vs gradual % rollout with metrics |
| Monitoring vs observability | Known-unknowns (alerts) vs unknown-unknowns (metrics+logs+traces) |
| SLI/SLO/SLA | Measured indicator / internal target / external contract |
| GitOps | Git as source of truth; in-cluster agent syncs and fixes drift |
| DORA metrics | Deploy frequency, lead time, MTTR, change failure rate |
