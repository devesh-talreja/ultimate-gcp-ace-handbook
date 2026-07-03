# Part 3 — DOMAIN 2: Planning & Implementing a Cloud Solution (~30%)

The biggest domain. Covers: 2.1 Compute, 2.2 Storage & data, 2.3 Networking, 2.4 Tooling. This is where AWS experience helps most — and where AWS instincts most often mislead you on specifics. Read the AWS tip boxes closely.

## 3.1 The Compute Decision Framework (memorize this first)

Exam objective 2.1 opens with: “Selecting appropriate compute choices for a given workload.” This single decision tree probably drives 5–10% of the whole exam by itself.

| Compute option | Best for | You manage | AWS analogue |
| --- | --- | --- | --- |
| Compute Engine (VMs) | Full OS control, legacy/lift-and-shift apps, custom kernels/licensing, specific machine/GPU needs, long-running stateful workloads | OS, patching, scaling config (via MIG), runtime | EC2 |
| GKE Standard | Complex/large container workloads needing fine-grained node & cluster control, custom networking, mixed workloads, cost optimization at scale | Node pools, upgrades (or automate), workload manifests | EKS (self-managed nodes / Fargate mix) |
| GKE Autopilot | Container workloads where you want Google to manage nodes entirely — pay per pod resource request, hardened defaults, less ops | Only the workload spec (Pods/Deployments) — no node management | EKS with Fargate (closest analogy) |
| Cloud Run (services) | Stateless HTTP(S) containers, request-driven, scale-to-zero, simplest serverless container experience | Just the container image + config | AWS App Runner / Fargate (HTTP-triggered) |
| Cloud Run (jobs) | Run-to-completion batch/task containers (not HTTP-triggered) | Container image + job config | AWS Fargate task / Batch |
| Cloud Run functions | Single-purpose event-driven functions (HTTP or event triggers: Pub/Sub, Storage, Eventarc) | Just function code | AWS Lambda |
| Agent Runtime (Gemini Enterprise Agent Platform) | Deploying/hosting AI agents (built with ADK/LangChain/etc.) with managed sessions, memory, scaling | Agent code/config | Bedrock AgentCore (closest AWS analogy) |

> [!TIP]
> **Exam Tip:** Decision shortcut for exam questions: Need full OS/root access or special licensing/GPU tuning? → Compute Engine. Need containers with heavy customization/networking control at scale? → GKE Standard. Want zero node management for containers? → GKE Autopilot. Simple stateless web service/API, want scale-to-zero and minimal ops? → Cloud Run. Single event-driven function reacting to an event? → Cloud Run functions. Deploying a Gen-AI agent? → Agent Runtime.

## 3.2 Compute Engine — Deep Dive

### Machine Types

| Family | Optimized for | Notes |
| --- | --- | --- |
| E2 | Cost-optimized, general purpose | Cheapest, good default for dev/test, shared-core options (e2-micro/small/medium) eligible for Always Free tier |
| N2 / N2D / N4 | Balanced price/performance, general purpose | N2D = AMD (cheaper), N4 = newest Intel gen, custom machine types supported |
| C3 / C4 | Compute-optimized | High-performance CPU workloads (HPC, gaming, ad serving) |
| M-series | Memory-optimized | In-memory DBs (SAP HANA), huge RAM:vCPU ratio |
| A-series / G-series | Accelerator-optimized (GPU) | AI/ML training & inference, A3/A4 = latest-gen for large model training |
| Tau T2A / T2D | ARM-based (T2A) / AMD (T2D) | Scale-out, ARM-compatible workloads (cost-efficient) |

- Custom machine types: define your own vCPU:RAM ratio (N-series) instead of a fixed shape — avoids overpaying for RAM/CPU you don't need. A close AWS parallel doesn't really exist (AWS has fixed instance families only) — this is a genuine GCP differentiator worth remembering.
> [!NOTE]
> **AWS ↔ GCP Tip:** No 1:1 AWS naming map exists, but roughly: E2≈T3/T4g burstable-ish (though E2 isn't burstable-credit-based, just cost-optimized), N2≈M6i, C3≈C6i, M-series≈X1e/R-series (memory), A-series≈P/G-series (GPU). Don't over-index on exact mapping — the exam tests choosing the right category, not exact AWS-equivalent names.

### Storage for Compute Engine

| Option | Key traits | When to use |
| --- | --- | --- |
| Zonal Persistent Disk (PD) | Block storage replicated within a single zone; types: pd-standard (HDD), pd-balanced (SSD, best default), pd-ssd (higher perf SSD), pd-extreme (very high IOPS) | Default boot/data disk; cheaper; tied to one zone (must snapshot to move zones) |
| Regional Persistent Disk | Synchronously replicated across 2 zones in a region | Higher availability without app-level replication; used behind regional MIGs for HA |
| Hyperdisk | Next-gen block storage, decoupled IOPS/throughput/capacity provisioning; variants: Hyperdisk Balanced, Extreme, Throughput, ML (for AI/ML read-heavy workloads) | Newest option; when you need to tune performance independent of disk size, or high-throughput ML data loading |
| Local SSD | Physically attached NVMe/SCSI SSD, ephemeral (data lost on stop/terminate/host maintenance without live migration) | Highest IOPS/lowest latency scratch space, caches, temp processing — NOT for durable data |

> [!NOTE]
> **AWS ↔ GCP Tip:** Zonal PD ↔ EBS (gp3/io2 territory). Regional PD ↔ no direct EBS equivalent (EBS is AZ-bound; you'd normally use app-level replication or Multi-Attach in AWS) — GCP's synchronous cross-zone block replication is a genuine differentiator. Local SSD ↔ EC2 Instance Store.

> [!CAUTION]
> **Common Mistake:** Deleting a VM by default also deletes its boot disk unless you explicitly set “Keep disk” / uncheck auto-delete on that disk. This trips people up when trying to preserve data by just stopping/deleting an instance.

### Instance Templates, MIGs & Autoscaling

- Instance template = immutable blueprint (machine type, image, disks, network, metadata/startup script) used to stamp out identical VMs.
- Managed Instance Group (MIG) = a set of identical VMs created from a template, with autoscaling, autohealing (via health checks — unhealthy instances auto-recreated), rolling updates, and multi-zone (regional MIG) distribution.
- Autoscaling policies: based on CPU utilization, load balancing serving capacity, Cloud Monitoring custom metrics, or schedule-based.
- Unmanaged instance group = arbitrary, dissimilar VMs grouped for load balancing only — no autoscaling/autohealing/template. Rarely the right exam answer when “autoscaling” or “autohealing” is mentioned.
> [!TIP]
> **Exam Tip:** “Configure re-creation of unresponsive VMs with the fewest steps” → MIG autohealing with a health check (not a load balancer health check alone — those only affect traffic routing, not recreation). This exact scenario appears often.

> [!NOTE]
> **AWS ↔ GCP Tip:** Instance Template + MIG ↔ Launch Template + Auto Scaling Group. MIG autohealing ↔ ASG health check replacement, but GCP's autohealing is explicitly decoupled from the load balancer's own health check — don't confuse the two health check types.

### Spot VMs, Custom Machine Types, Availability Policy

- Spot VMs = current name for deeply discounted (up to ~60–91% off), reclaimable-anytime capacity (replaces the older “Preemptible VM” model — Preemptible still exists as a fixed 24h-max legacy option; Spot has no fixed runtime limit but can still be preempted with only a 30-second termination notice).
- Use Spot for fault-tolerant, stateless, batch/CI, big-data processing — never for stateful/production-critical single points of failure.
- Availability Policy settings on a VM: On host maintenance = Migrate (live-migrate the VM to another host during Google maintenance, default for standard VMs) vs. Terminate (required for GPU-attached VMs and Spot VMs — they cannot live-migrate). Automatic restart = whether GCP restarts the VM after a non-user-initiated termination (crash), independent of live migration.
> [!NOTE]
> **AWS ↔ GCP Tip:** Spot VM ↔ EC2 Spot Instance. Key nuance difference: GCP VMs live-migrate by default during host maintenance (near-zero downtime, no interruption) — AWS EC2 has no live migration; instead AWS just reboots/replaces during maintenance events. This live migration feature is GCP-distinctive and a favorite “what happens during host maintenance” exam question.

### SSH Access, OS Login, VM Manager

- Metadata-based SSH keys: add a public key to instance/project metadata; GCP injects it into the VM's authorized_keys — simple but not tied to IAM, hard to audit ‘who accessed which VM,’ keys can sprawl.
- OS Login (recommended): ties SSH access to the user's Google identity + IAM role (roles/compute.osLogin or osAdminLogin for sudo). Enables centralized, IAM-governed, audit-logged SSH access — revoke IAM role = revoke SSH access instantly, no key cleanup needed.
- IAP (Identity-Aware Proxy) TCP forwarding is often paired with OS Login to SSH into VMs without any external IP or open firewall port — tunnels through IAP using IAM authorization.
- VM Manager = a suite for fleet OS management: OS Config Patch Management (scheduled OS patching), OS Inventory Management (installed software/OS reporting), OS Policies (declarative config compliance).
> [!TIP]
> **Exam Tip:** The exact scenario from objective 1 above (“team needs only admin access, must be operationally efficient, must audit who accessed an instance”) → answer: each user adds their own SSH key to their own Google account, grant the roles/compute.osAdminLogin (osAdminLogin) role to the Google Group — i.e., OS Login + Groups, NOT manually distributing/rotating metadata SSH keys.

> [!NOTE]
> **AWS ↔ GCP Tip:** OS Login ↔ EC2 Instance Connect / SSM Session Manager (IAM-governed, keyless-feeling access). VM Manager (patch/inventory/policy) ↔ AWS Systems Manager (Patch Manager, Inventory, State Manager) — a very close conceptual match.

---

> [◀ Part 2b: Operations, Networking & Billing](./part-02b-operations-networking-billing.md) | [🏠 Back to README](../README.md) | [Part 3b: GKE & Serverless Compute ▶](./part-03b-gke-and-serverless.md)
