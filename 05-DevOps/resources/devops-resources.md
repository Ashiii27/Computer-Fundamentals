# DevOps — Curated Learning Resources

> Roadmaps → Books → Docs & Tutorials → YouTube → Free hands-on labs. Starred items = best value.

## Roadmaps (start here)
| Resource | Why | Link |
|---|---|---|
| **roadmap.sh — DevOps** | The canonical skill tree with linked resources per node | https://roadmap.sh/devops |
| **roadmap.sh — Linux / Git / Docker** | Deep-dive maps per skill | https://roadmap.sh |

## Books
| Resource | Why | Link |
|---|---|---|
| **The Phoenix Project** — Kim, Behr, Spafford | DevOps culture as a novel — the why in story form | Print / IT Revolution |
| **The DevOps Handbook** — Kim et al. | The how: CI/CD, telemetry, the Three Ways | Print / IT Revolution |
| **Continuous Delivery** — Humble & Farley | The bible of deployment pipelines | Print / Addison-Wesley |
| **Site Reliability Engineering (SRE)** — Google | **Free to read online**; SLOs, error budgets, toil | https://sre.google/books/ |
| **Accelerate** — Forsgren, Humble, Kim | The science behind the DORA metrics | Print / IT Revolution |

## Official Docs & Tutorials (learn from the source)
| Resource | Why | Link |
|---|---|---|
| **Docker docs — Get Started** | Best-in-class official tutorial (build/run/push, compose) | https://docs.docker.com/get-started/ |
| **Kubernetes docs — Tutorials** | Concepts + interactive basics | https://kubernetes.io/docs/tutorials/ |
| **Terraform tutorials** | HashiCorp's official guided path | https://developer.hashicorp.com/terraform/tutorials |
| **Ansible docs — Getting started** | Playbooks, inventory, idempotency | https://docs.ansible.com/ansible/latest/getting_started/index.html |
| **GitHub Actions docs** | Workflow syntax, quickstarts | https://docs.github.com/actions |
| **Jenkins user documentation** | Pipelines, agents, Jenkinsfile | https://www.jenkins.io/doc/ |
| **Pro Git** (free book) | Everything Git, free | https://git-scm.com/book |
| **Learn Git Branching** (interactive) | Visual rebase/merge playground | https://learngitbranching.js.org |
| **The Twelve-Factor App** | The methodology behind cloud-native apps | https://12factor.net |
| **DORA (research + metrics)** | DevOps performance research & guides | https://dora.dev |

## YouTube
| Channel / Playlist | Best for | Link |
|---|---|---|
| **TechWorld with Nana** | Complete Docker/K8s/CI-CD courses, beginner-friendly | https://www.youtube.com/@TechWorldwithNana |
| **freeCodeCamp — DevOps courses** | Multi-hour full courses (Docker, K8s, Terraform, CI/CD) | https://www.youtube.com/results?search_query=freecodecamp+devops |
| **NetworkChuck** | Linux, Docker, networking with high energy | https://www.youtube.com/@NetworkChuck |
| **KodeKloud** | K8s/IaC tutorials (channel free; labs paid) | https://www.youtube.com/@KodeKloud |

## Free Hands-on Labs (do these, don't just watch)
| Lab | What you practice | Link |
|---|---|---|
| **Play with Docker** | Free temporary Docker environment in browser | https://labs.play-with-docker.com |
| **Killercoda** | Free K8s/Linux/Terraform interactive scenarios | https://killercoda.com |
| **Play with Kubernetes** | Multi-node K8s cluster in browser | https://labs.play-with-k8s.com |
| **OverTheWire — Bandit** | Linux/shell skills as a wargame | https://overthewire.org/wargames/bandit/ |
| **Local**: `minikube` / `kind` + Docker Desktop + Terraform free tier | Real local cluster + cloud practice | https://minikube.sigs.k8s.io |

## Certification path (optional, useful for resumes)
1. Any cloud associate (AWS SAA / Azure AZ-104) → 2. **CKA** (Certified Kubernetes Administrator) → 3. HashiCorp Terraform Associate → 4. Security+ / DevSecOps later.

## Suggested path
1. **Foundations:** Linux (Bandit) + Git (Learn Git Branching) + one Nana Docker course → run containers yourself.
2. **CI/CD:** build a pipeline for your own project (GitHub Actions): test → build image → push → deploy to a free tier.
3. **Orchestration:** K8s via Killercoda + local minikube; deploy your project with a Deployment + Service + Ingress.
4. **IaC & beyond:** Terraform tutorials (free tier cloud) → add Prometheus/Grafana → read *The Phoenix Project* for the culture.
