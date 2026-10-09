# Cloud Computing — Complete Revision Notes

> Covers: Cloud basics & NIST → Service models → Deployment models → Virtualization → Core services (compute/storage/network/identity) → HA & scaling → Serverless → Cost → Security

## Table of Contents
1. [What is Cloud Computing?](#1-what-is-cloud-computing)
2. [Service Models (SPI)](#2-service-models-spi)
3. [Deployment Models](#3-deployment-models)
4. [Virtualization & Containers](#4-virtualization--containers)
5. [Core Cloud Services](#5-core-cloud-services)
6. [High Availability & Scaling](#6-high-availability--scaling)
7. [Serverless](#7-serverless)
8. [Cloud Economics](#8-cloud-economics)
9. [Cloud Security](#9-cloud-security)
10. [Trends](#10-trends)
11. [Cheat Sheet](#11-cheat-sheet)

---

## 1. What is Cloud Computing?

**On-demand delivery of IT resources (compute, storage, networks) over the internet with pay-as-you-go pricing** — renting a data center as a utility instead of owning one.

### NIST's 5 essential characteristics (memorize)
1. **On-demand self-service** — provision resources via console/API, no human approval
2. **Broad network access** — standard protocols, any device
3. **Resource pooling** — multi-tenant hardware abstracted per customer
4. **Rapid elasticity** — scale out/in automatically with demand (the differentiator!)
5. **Measured service** — metered usage → pay-per-use

### Why organizations move
✅ CapEx→OpEx, no idle hardware, global reach in minutes, managed services (DBs, ML, queues), elasticity.
⚠️ Concerns: cost governance (surprise bills!), vendor lock-in, data sovereignty/compliance, network dependency, shared-security misunderstandings.

---

## 2. Service Models (SPI)

| Model | You manage | Provider manages | Examples | Analogy |
|---|---|---|---|---|
| **On-prem** | Everything (apps, runtime, OS, virtualization, servers, storage, networking) | — | Your own DC | Owning a car |
| **IaaS** (Infrastructure) | Apps, data, runtime, **OS** | Virtualization, servers, storage, networking | EC2, Azure VMs, GCE | Renting a car |
| **PaaS** (Platform) | Apps + **data only** | Runtime, OS, everything below | Heroku, App Engine, Elastic Beanstalk, App Service | Taking a taxi |
| **SaaS** (Software) | Just your data & settings | Everything | Gmail, Salesforce, M365, Zoom | Taking a bus |
| **FaaS** (serverless) | Function code | All infra + scaling | Lambda, Cloud Functions, Azure Functions | Hop-on hop-off |

Rule of thumb: going up the stack (IaaS→SaaS), **you manage less, provider manages more**; less control but faster delivery.

---

## 3. Deployment Models

| Model | Description | When |
|---|---|---|
| **Public** | Provider-owned, multi-tenant (AWS/Azure/GCP) | Default for most workloads |
| **Private** | Single-org cloud (on-prem OpenStack, VMware) | Strict compliance/latency/data residency |
| **Hybrid** | Public + private connected (burst to cloud, keep sensitive on-prem) | Regulated data + cloud elasticity |
| **Multi-cloud** | 2+ public providers | Leverage, resilience, M&A legacy — costs: skills, security sprawl, no egress unity |

---

## 4. Virtualization & Containers

- **Hypervisor** — software that multiplexes physical hardware into VMs.
  - **Type 1 (bare-metal)**: runs directly on hardware — ESXi, Hyper-V, KVM/Xen (used by clouds).
  - **Type 2 (hosted)**: runs on a host OS — VirtualBox, VMware Workstation.
- **VM vs container**: VM = full guest OS per instance (strong isolation, GBs, slow boot); container = shared host kernel, isolated by namespaces/cgroups (MBs, ms boot). See DevOps notes for the Docker deep-dive.
- Cloud connection: **most IaaS VMs are themselves KVM/Xen VMs**; virtualization → cloud was a 2-step story (abstraction of hardware → pooling + self-service + metering).
- Modern twist: **microVMs & serverless** (Firecracker powers Lambda) give VM-grade isolation with container-grade speed.

---

## 5. Core Cloud Services

> Naming below is AWS-first with Azure/GCP equivalents — the concepts matter more than brand names.

### Compute
- **VMs**: EC2 / Azure VM / Compute Engine — pick instance family (general, compute-optimized, memory-optimized, GPU), size, AMI image.
- **Autoscaling groups**: add/remove VMs on metrics.
- **Load balancers**: distribute traffic (L4 network vs L7 application).
- **Containers**: EKS/AKS/GKE (managed Kubernetes), ECS/Fargate.
- **Serverless**: Lambda/Functions (see §7).

### Storage — the 3 flavors (very common interview question)
| Type | Structure | Latency | Use | Examples |
|---|---|---|---|---|
| **Object** | Flat buckets; objects + metadata; HTTP API; virtually unlimited | ms | Backups, media, static sites, data lakes | S3 / Blob Storage / Cloud Storage |
| **Block** | Raw disk volumes attached to one VM | µs–ms | OS disks, databases, boot volumes | EBS / Azure Disk / Persistent Disk |
| **File** | Hierarchical shared filesystem (NFS/SMB) | ms | Shared app storage, CMS, analytics | EFS, FSx / Azure Files / Filestore |

- **S3 storage classes**: Standard (frequent) → Standard-IA / Intelligent-Tiering → Glacier Instant/ Flexible/Deep Archive (compliance archives, hours to retrieve) — pay per access pattern; 11 nines (99.999999999%) durability on Standard.
- **Databases managed for you**: relational (RDS/Azure SQL/Cloud SQL), NoSQL (DynamoDB/Cosmos DB/Firestore), caching (ElastiCache/Memorystore), warehouse (Redshift/Synapse/BigQuery).

### Networking
- **VPC** (Virtual Private Cloud): your private network → subnets across AZs (public = internet-routable, private = internal), route tables.
- **Internet Gateway** (public internet), **NAT Gateway** (outbound-only for private subnets), **peering/Transit Gateway** (VPC-to-VPC), **Direct Connect/ExpressRoute** (private dedicated lines to on-prem).
- **Security groups** (instance-level, **stateful**, allow-rules only) vs **NACLs** (subnet-level, **stateless**, allow+deny, evaluated in order).
- **Route 53/DNS**, **CloudFront/CDN** (edge caching), **API Gateway** (managed front door).

### Identity & access
- **IAM**: users (long-term credentials), **groups**, **roles** (assumable identities for services/humans — *preferred*, no static keys), **policies** (JSON allow/deny on actions+resources). Least privilege always.
- MFA, federation/SSO (OIDC/SAML), audit via CloudTrail/Activity Logs.

---

## 6. High Availability & Scaling

- **Region** = geographic cluster of data centers (e.g., Mumbai ap-south-1). **Availability Zone (AZ)** = isolated data center(s) inside a region with independent power/network; low-latency links between AZs. **Edge locations** = CDN PoPs.
- HA recipe: multi-AZ by default (2+ AZs), multi-region for DR on critical systems.
- **Scaling**:
  | Vertical (scale up) | Horizontal (scale out) |
  |---|---|
  | Bigger machine (8→32 GB) | More machines (2→20) |
  | Simple; limit = biggest box; downtime to resize | Near-unlimited; needs stateless design + LB |
  | DBs often scale up first | Stateless app tier scales out |
- **Elasticity vs scalability**: scalability = *can* grow; elasticity = grows/shrinks *automatically with demand*.
- **Auto scaling**: policies on metrics (CPU > 70% → +2 instances), scheduled (known peaks), predictive.
- **Load balancer algorithms**: round-robin, least-connections, IP hash; health checks evict dead targets.
- **DR terms**: **RPO** — max tolerable data loss (backup frequency: "we can lose 15 min"). **RTO** — max tolerable recovery time ("we're back in 2 hours"). DR patterns: backup&restore (cheapest/slowest) → pilot light → warm standby → multi-site active-active (fastest/costliest).
- Stateless design (state in managed DBs/object storage) is what makes horizontal scaling & self-healing work — ties to 12-factor app.

---

## 7. Serverless

- **FaaS**: upload functions; the platform runs, scales (0→thousands), patches, and bills per **invocation × duration**. Lambda/Cloud Functions/Azure Functions.
- **Pros**: zero ops, automatic elasticity, pay only for use (great for spiky/idle workloads), forced microservice decomposition.
- **Cons**: **cold starts** (first invocation latency while a sandbox boots — mitigate with provisioned concurrency/warmers), execution time/memory limits, vendor coupling, harder local testing/debugging, cost inversion for steady high traffic (VMs cheaper at scale).
- **BaaS** (the other serverless): managed auth (Cognito), storage (S3), DB (DynamoDB) — "serverless" ≠ only functions.
- Event-driven glue: S3 upload → trigger Lambda → write to DynamoDB → notify via SNS. Queues: SQS (queue) vs SNS (pub/sub fanout) vs Kinesis (ordered streams).

---

## 8. Cloud Economics

- **Pricing models**: on-demand (flexible, highest rate), **reserved** (1–3 yr commitment, up to ~60-70% off — steady workloads), **spot** (spare capacity, up to ~90% off, can be reclaimed with 2-min notice — batch/fault-tolerant only), savings plans (flexible commitment).
- TCO: cloud isn't automatically cheaper — it converts CapEx to OpEx and removes idle capacity; savings come from right-sizing + turning things off.
- **FinOps**: tagging resources, budgets/alerts, rightsizing reports, shutting down dev at night, storage lifecycle policies (S3 → Glacier), spot for CI.
- Free tiers: AWS 12-month/always-free, Azure/GCP credits — enough to run every lab in the resources file.

---

## 9. Cloud Security

### Shared responsibility model (know this cold)
| Layer | IaaS | SaaS |
|---|---|---|
| Provider secures | Physical, network, hypervisor, (IaaS: none of OS+) | Everything incl. application |
| **Customer secures** | Guest OS, patches, app, data, IAM config | Data, identities, device, config (who can see what) |

"My data leaked" is almost always a customer-side misconfiguration (public S3 bucket, open security group) — the provider's layer held. Security *of* the cloud vs security *in* the cloud.

- **Encryption**: at rest (KMS-managed keys, transparent) + in transit (TLS everywhere). Customer-managed keys for regulated data.
- Hardening checklist: least-privilege IAM, MFA + no root usage, private subnets for data tiers, SG deny-all-default, logging (CloudTrail/VPC flow logs), backups + restore drills, secret managers instead of hardcoded keys.
- Zero-trust pointer: never trust network location; authenticate+authorize every call (see Cybersecurity notes).

---

## 10. Trends

- **Edge computing**: compute near users/devices (CDN workers, IoT gateways) — latency-sensitive + data-sovereign workloads.
- **Cloud-native**: containers + orchestration + microservices + CI/CD + observability as the default way to build (see DevOps notes).
- **Kubernetes as portability layer** — the de-facto answer to lock-in between clouds.
- **AI/ML platforms**: managed GPUs, model training/serving (SageMaker, Vertex AI) — the current growth engine.
- **Sovereign & sustainability**: data-residency regions, green-cloud (carbon dashboards, spot reuse).

---

## 11. Cheat Sheet

| Concept | One-liner |
|---|---|
| Cloud | On-demand, elastic, metered IT over the internet |
| NIST 5 | On-demand, broad access, pooling, **elasticity**, measured |
| IaaS/PaaS/SaaS | Manage OS/runtime/data → app/data → just data & settings |
| Hypervisor types | Type 1 bare-metal (clouds), Type 2 hosted (VirtualBox) |
| Object vs block vs file | Buckets+HTTP / raw disk for VMs&DBs / shared NFS |
| S3 durability | 11 nines (99.999999999%) on Standard |
| Region vs AZ | Geography vs isolated DC inside it — deploy across AZs |
| Scale up vs out | Bigger box vs more boxes (needs stateless + LB) |
| Scalability vs elasticity | Can grow vs auto grows/shrinks |
| SG vs NACL | Instance, stateful, allow-only vs subnet, stateless, allow+deny |
| IAM role vs user | Assumable, temp credentials (preferred) vs long-term keys |
| RPO vs RTO | Max data loss vs max recovery time |
| Serverless pros/cons | No ops, pay-per-use vs cold starts, limits, lock-in |
| Spot vs reserved | ~90% off interruptible vs 1–3 yr committed discount |
| Shared responsibility | Provider: *of* the cloud. You: *in* the cloud (data, IAM, config) |
