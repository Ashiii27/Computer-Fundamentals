# Cloud Computing — Top 25 Interview Questions (with Answers)

> Concept-heavy answers sized for spoken interviews; sprinkle provider examples (AWS/Azure/GCP) for credibility.

---

### 1. What is cloud computing? What makes it different from a traditional data center?
On-demand delivery of compute/storage/networking over the internet with pay-as-you-go pricing. Differences: provision in minutes via API instead of procurement cycles, elastic scaling with demand instead of fixed capacity, metered billing instead of CapEx, and managed services that offload undifferentiated heavy lifting (patching, backups, replication).

### 2. Name the 5 NIST characteristics of cloud.
On-demand self-service, broad network access, resource pooling (multi-tenancy), **rapid elasticity**, and measured service (pay-per-use). Elasticity is the one that most separates cloud from classic hosting.

### 3. IaaS vs PaaS vs SaaS — with examples.
IaaS: raw virtual infrastructure; you manage OS up — EC2/Azure VM. PaaS: managed runtime; you deploy code and data only — Heroku, App Engine, Elastic Beanstalk, App Service. SaaS: finished software; you configure and use — Gmail, Salesforce, Zoom. Management burden shrinks IaaS→SaaS; control shrinks too. (Bonus: FaaS/Lambda = serverless, one step further than PaaS.)

### 4. Public vs private vs hybrid cloud?
Public: provider-owned, multi-tenant (AWS/Azure/GCP) — default choice, best elasticity/cost. Private: dedicated single-org cloud for strict compliance, residency, or latency needs (costlier to run well). Hybrid: both, connected — keep regulated data on-prem while bursting elastic workloads to public. Multi-cloud (2+ providers) adds resilience/vendor leverage at the cost of skills and security sprawl.

### 5. What is virtualization? Hypervisor types?
Virtualization abstracts physical hardware into multiple isolated VMs via a **hypervisor**. Type 1 (bare-metal) runs directly on hardware — ESXi, KVM/Xen (what clouds actually run). Type 2 (hosted) runs atop a normal OS — VirtualBox. Clouds are built on Type-1 virtualization; containers then virtualize at the OS level (namespaces/cgroups) for lighter-weight isolation.

### 6. VM vs container in the cloud?
VM: hypervisor-virtualized hardware, full guest OS — GBs, minute-level boot, strongest isolation; right for strict tenancy or legacy stacks. Container: shared host kernel, process-level isolation — MBs, millisecond boot, dense scheduling; the unit of modern cloud-native deployment. Serverless (FaaS) hides even the container, executing functions with per-invocation billing.

### 7. Region vs Availability Zone — why does the distinction matter?
Region: a geographic area (e.g., Mumbai) containing multiple AZs. AZ: one or more physically separate data centers with independent power/network but low-latency links inside the region. It matters because HA design = spread across ≥2 AZs (survives a DC failure) while keeping latency low; multi-region is for DR, latency, or data-residency. Choosing a region = latency to users + compliance + cost + service availability.

### 8. Vertical vs horizontal scaling?
Vertical (scale up): move to a bigger instance — simple, no app changes, but hard limits and resize downtime; databases often start here. Horizontal (scale out): add more instances behind a load balancer — near-linear, fault-tolerant, pay-as-you-grow, but requires **stateless** application design and session/externalized state. Cloud best practice: design stateless, scale horizontally, let autoscaling handle demand.

### 9. What is autoscaling and how does it decide?
Automatic addition/removal of instances based on **target metrics** (CPU > 70% → add; < 30% → remove), schedules (weekday peaks), or predictive policies. Paired with a load balancer + health checks so new instances join and dead ones drain. Benefits: elasticity (pay for peak only when at peak) and self-healing capacity.

### 10. Object vs block vs file storage?
Object (S3/Blob/GCS): flat buckets, objects + rich metadata over HTTP, virtually unlimited, cheap — backups, media, data lakes; slight per-request latency. Block (EBS/Disk): raw volumes attached to a VM, lowest latency — boot disks and databases. File (EFS/Azure Files): shared hierarchical filesystem over NFS/SMB — shared app data across many VMs. Rule of thumb: DBs → block, share → file, everything else at scale → object.

### 11. What are S3 storage classes and why do they exist?
Tiers priced per access pattern: Standard (frequent), Standard-IA/One-Zone-IA (rare access), Intelligent-Tiering (unknown patterns, auto-moves), Glacier Instant/Flexible/Deep Archive (archives — cheap storage, retrieval fees/time). Use lifecycle policies to age data downward automatically; this is where storage bills actually shrink.

### 12. Security group vs NACL?
Security group: **instance** level, **stateful** (reply traffic auto-allowed), allow-only rules — the default choice. NACL: **subnet** level, **stateless** (must allow both directions explicitly), supports allow **and** deny with rule order — good for explicit blocks (e.g., deny a bad IP range). Best practice: SGs per tier (web/db), NACLs as coarse subnet guardrails.

### 13. IAM user vs role — which should services use?
User: long-lived identity with permanent keys — humans (with MFA) and unavoidable cases only. Role: assumable identity granting **temporary** credentials — the right choice for services and cross-account access (Lambda's execution role, EC2 instance profile). Roles eliminate hardcoded keys, the #1 cloud security smell; humans get SSO/federated roles too.

### 14. Explain the shared responsibility model.
The provider secures **of** the cloud: facilities, hardware, hypervisor (IaaS), plus OS/runtime/app (SaaS). The customer secures **in** the cloud: guest OS patching, application code, IAM permissions, encryption choices, and — most critically — configuration and their data. Public S3-bucket leaks are customer-side misconfigurations, which is why this model is interview-critical.

### 15. What is a VPC? Walk through its main components.
Your logically isolated network in the cloud: **subnets** (public: route to Internet Gateway; private: no direct inbound), **route tables** deciding traffic flow, **security groups/NACLs** filtering, **Internet Gateway** for public access, **NAT Gateway** so private subnets can patch outbound-only, **peering/Transit Gateway** connecting VPCs, and optionally **Direct Connect/VPN** linking on-prem. Typical 3-tier app: public subnet for LB, private for app and DB tiers.

### 16. What is serverless computing? Pros and cons?
You deploy functions (or use managed services); the provider runs, scales (including to zero), patches, and bills per invocation×duration. Pros: zero server ops, automatic elasticity, pay-per-use — perfect for spiky/idle workloads and glue logic. Cons: cold-start latency, execution time/memory limits, deeper vendor coupling, trickier debugging, and worse economics for steady high-volume loads. Classic pattern: API Gateway + Lambda + DynamoDB.

### 17. What are cold starts and how do you mitigate them?
The latency of the first request while the platform allocates and initializes your function's sandbox (runtime + your init code). Mitigations: provisioned concurrency (pre-warmed instances), slim runtimes/deps, lazy initialization, keep-alive connections, or moving that path to a container/VM when p99 matters. Most workloads tolerate occasional cold starts fine.

### 18. Load balancer types — L4 vs L7?
L4 (network): routes on IP/port/protocol — fast, protocol-agnostic. L7 (application): understands HTTP — path/host-based routing, TLS termination, sticky sessions, header rewriting, WAF integration. Cloud defaults: ALB (L7) for microservices/HTTP, NLB (L4) for extreme performance/TCP, Gateway LB for appliances. Health checks remove unhealthy targets automatically.

### 19. What is a CDN and why use it?
A geographically distributed set of edge caches (CloudFront, Akamai) serving static content from the location nearest each user — cutting latency, offloading origin traffic, absorbing DDoS/spikes, and enabling TLS termination at the edge. Also cache dynamic content fragments and terminate connections closer to mobile users. Cache invalidation and appropriate TTLs are the operational gotchas.

### 20. RPO vs RTO?
**RPO**: maximum tolerable data loss measured backwards — how stale data may be when you recover (drives backup/replication frequency: 15-min RPO → async replication or frequent snapshots). **RTO**: maximum tolerable recovery time — how long until service is back (drives standby architecture). Together they pick your DR pattern: backup&restore (hours, cheap) → pilot light → warm standby → active-active (minutes, expensive).

### 21. Reserved vs spot vs on-demand instances?
On-demand: pay-per-hour, zero commitment — spiky/unpredictable. Reserved/Savings Plans: 1–3-year commitment, up to ~60-70% off — steady baseline workloads. Spot: provider's spare capacity up to ~90% off but reclaimable on short notice — batch, CI, stateless workers that checkpoint. The mature answer: baseline on reserved, spiky on-demand, interruptible on spot.

### 22. What is vendor lock-in and how do you reduce it?
Dependency on one provider's proprietary services making exit costly. Mitigations: prefer open standards (containers/Kubernetes, Postgres/MySQL engines, Terraform over point-and-click), keep data in portable formats, abstract provider SDKs behind interfaces, document egress costs up front. Trade-off honestly: managed proprietary services save months — lock-in is a business decision, not always a mistake.

### 23. Why do cloud architectures prefer stateless applications?
State (sessions, files) lives in external managed stores (Redis, DBs, object storage) instead of instance memory/disk — then any instance can serve any request, which unlocks horizontal autoscaling, zero-downtime rolling deploys, blue-green switches, and self-healing (dead instance = lost nothing). This is the same principle as the 12-factor app.

### 24. How would you migrate an on-prem app to the cloud? (The 6 Rs)
**Rehost** (lift-and-shift: VMs as-is — fastest), **Replatform** (minor wins: managed DB, containers), **Refactor** (re-architect cloud-native — most value, most effort), **Repurchase** (drop to SaaS), **Retire** (kill dead systems), **Retain** (keep on-prem for now). Typical strategy: portfolio-triage per app; start with rehost for quick wins, refactor the crown jewels where elasticity/modernization pays.

### 25. How do you keep cloud costs under control?
Tag everything (owner/env/cost-center) → budgets and anomaly alerts → rightsizing off utilization reports → kill idle resources (nightly dev shutdown, unattached volumes) → storage lifecycle to cold tiers → spot where tolerable → FinOps reviews as a routine. The classic answer they want: visibility first (tagging/reporting), then automation (policies, schedulers), then culture (cost is an engineering metric).

---

## How to answer cloud questions well
- Answer conceptually first, then give provider examples ("that's S3 in AWS, Blob in Azure…").
- Always attach trade-offs (serverless vs VMs, lock-in vs managed services).
- For design-y prompts, structure: requirements → region/AZ layout → compute/storage/network → security/IAM → scaling/DR → cost.
