# Part 3d: VPC Networking & Planning Tools

## 3.10 Networking — Deep Dive

### VPC & Subnets

- A GCP VPC is global; subnets are regional (span all zones in that region automatically — you don't pick a zone for a subnet, unlike AWS where a subnet = one AZ).
- **Auto mode VPC:** Google automatically creates one subnet per region with predefined ranges — quick start, not recommended for production (limited control, easy to hit overlaps).
- **Custom mode VPC:** you define subnets, regions, and CIDR ranges yourself — recommended for production (matches objective 2.3's explicit “custom mode VPC” language).
- **Shared VPC:** a host project owns the VPC/subnets and shares them with one or more service projects — centralizes network administration while letting app teams deploy resources into their own projects using the shared network. IAM for network admin stays with the host project's network admins.
- **VPC Network Peering:** connects two VPCs (even across projects/orgs) privately using internal IPs, non-transitive (if A peers with B and B peers with C, A cannot reach C through B).
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** VPC (global) is the single biggest networking mindset shift from AWS — re-read this until it's intuitive. Shared VPC ↔ AWS VPC sharing (RAM-shared subnets) — conceptually similar (central network, app teams deploy into it). VPC Peering ↔ AWS VPC Peering, same non-transitive limitation in both clouds.

> [!TIP]
> 💡 **Exam Tip:** Shared VPC vs. VPC Peering — pick Shared VPC when you want centralized network control/policy across many teams/projects in one org (hub-and-spoke-ish, admin-controlled). Pick Peering when you need to connect a smaller number of independently-managed VPCs (including across different orgs) without centralizing admin control.

### Firewalls: VPC Firewall Rules vs. Cloud NGFW Policies

- **VPC firewall rules:** the classic model — rules attached directly to a VPC network, each with direction (ingress/egress), action (allow/deny), priority, source/destination (IP range, Tags, or service account), protocol/port, and target (all instances, or instances matching a Tag/service account).
- **Cloud NGFW (Next Generation Firewall):** the newer, broader firewall product family, offering policies you can apply globally, regionally, or hierarchically (at Org/Folder level, enforced consistently down the resource hierarchy) — plus advanced Layer 7 features.
- **Cloud NGFW tiers:** Essentials (free — standard L3/L4 rules by IP/port/protocol, i.e. what classic VPC firewall rules already offered), Standard (adds FQDN objects + threat intelligence feeds), Enterprise (adds Layer 7 URL filtering + intrusion detection/prevention — full “next-gen” inspection).
- **Hierarchical firewall policies:** applied at Org/Folder level, enforced on all projects/VPCs beneath — used for org-wide security baselines (e.g., “never allow inbound from the internet to port 22 anywhere”) that individual project teams cannot override.
- **Tags (the modern “secure Tags”** — IAM-governed key:value resource tags) and service accounts can both be used as firewall rule/policy targets and sources, letting you write identity-based rules (“allow traffic only from resources running as this service account”) instead of brittle IP-based rules.
> [!TIP]
> 💡 **Exam Tip:** Hierarchy of enforcement (all evaluated together, most restrictive wins for a given priority tier): Hierarchical firewall policies (Org/Folder) → Global/Regional network firewall policies → VPC firewall rules, each with their own priority ordering. Know that hierarchical policies exist specifically so a central security team can enforce rules app teams can't bypass.

> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** VPC firewall rules ↔ Security Groups (though GCP rules attach to the network/Tags/SAs, not to individual ENIs like an SG does) + a bit of NACLs (since GCP firewall rules are stateful like SGs but evaluated more like a flat rule list). Cloud NGFW Enterprise (L7, IDS/IPS, URL filtering) ↔ AWS Network Firewall. There's no exact single AWS analogue for the Essentials/Standard/Enterprise tiering — that's a GCP-specific packaging.

### Network Connectivity

| Option | What it is | Best for |
| --- | --- | --- |
| Cloud VPN | IPsec VPN tunnel over the public internet (Classic, or HA VPN for 99.99% SLA with redundant tunnels) | Quick setup, lower cost, moderate bandwidth needs, redundancy via HA VPN |
| Dedicated Interconnect | Direct physical private connection into Google's network (at a colocation facility) | High bandwidth (10/100 Gbps circuits), consistent low latency, large steady traffic — you own the physical link |
| Partner Interconnect | Private connection via a supported service provider (no need to be at a Google colocation facility) | Lower bandwidth needs than Dedicated, or your location isn't at a Google colo |
| VPC Network Peering | Private, internal-IP connectivity between two VPCs | Connecting VPCs (including across projects/orgs) without traffic touching the internet |
| Cross-Cloud Interconnect | Direct connection to another cloud provider (AWS/Azure/etc.) | Multi-cloud architectures needing private, high-bandwidth cloud-to-cloud links |

> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Cloud VPN ↔ AWS Site-to-Site VPN. Dedicated/Partner Interconnect ↔ AWS Direct Connect (Dedicated ↔ Dedicated Connection, Partner ↔ Hosted Connection via an APN partner). The naming logic maps closely once you see the pattern.

### Load Balancers — The Full Decision Tree

| Load balancer | Scope | Layer | Best for |
| --- | --- | --- | --- |
| Global external Application LB | Global | L7 (HTTP/S) | Internet-facing web apps/APIs needing global anycast IP, single IP routing to nearest healthy backend worldwide, URL/host-based routing |
| Regional external Application LB | Regional | L7 (HTTP/S) | Internet-facing HTTP(S) apps that only need to live in/serve one region (regulatory or simplicity reasons) |
| Internal Application LB | Regional (or cross-region) | L7 (HTTP/S) | Internal microservices needing HTTP(S) L7 routing (path/host rules) without internet exposure |
| External passthrough Network LB | Regional | L4 (TCP/UDP) | Non-HTTP(S) TCP/UDP traffic from the internet, need to preserve client IP, ultra-low latency, protocols LB shouldn't terminate |
| Internal passthrough Network LB | Regional | L4 (TCP/UDP) | Internal-only TCP/UDP load balancing (e.g., internal DB proxy tier) |
| SSL Proxy LB | Global | L4 (TCP+TLS termination) | Non-HTTP(S) TCP traffic that still needs global anycast + TLS termination at the edge |
| TCP Proxy LB | Global | L4 (TCP) | Global TCP traffic (non-TLS) needing a single global anycast IP |

- **Decision shortcut:** HTTP(S) traffic → Application (L7) LB (choose Global vs Regional vs Internal by exposure/scope needed). Non-HTTP TCP/UDP, need client IP preserved or protocol passthrough → Network (passthrough) LB. Non-HTTP TCP but want a single global anycast IP and/or TLS termination at Google's edge → Proxy LB (SSL or TCP).
- All Google Cloud load balancers are software-defined (no pre-warming needed like classic ELB, though extreme traffic spikes can still benefit from a documented “spike prep” in edge cases) and integrate natively with MIGs, GKE Ingress/Gateway, Cloud Run, and Cloud Storage buckets (for the Global external LB, serving static content directly).
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Global external Application LB ↔ no single AWS equivalent (closest: CloudFront + ALB, since AWS ALBs are always regional) — GCP's single global L7 IP for HTTP(S) is a genuine differentiator. External passthrough Network LB ↔ AWS NLB. Internal LBs ↔ AWS internal ALB/NLB. There's no AWS analogue exactly matching Proxy LB's global-TCP-with-TLS-termination niche.

### Network Service Tiers

| Tier | Routing | Best for |
| --- | --- | --- |
| Premium Tier | Traffic enters/exits via Google's global private backbone as close to the source/destination as possible (cold-potato routing) | Best performance, lowest latency/loss, production internet-facing workloads — default tier |
| Standard Tier | Traffic uses regional networking/public internet more like a typical ISP path (hot-potato routing), only within the same region as the resource | Lower cost, latency-tolerant/dev/test workloads, same-region-only traffic |

> [!TIP]
> 💡 **Exam Tip:** Standard Tier is cheaper but has restrictions (e.g., generally not usable with global load balancers, no multi-region acceleration) — a classic “cost optimization” exam scenario: “dev/test workload, cost is the top priority, latency doesn't matter” → Standard Tier.

## 3.11 Planning & Implementing with Tooling (Objective 2.4)

### Infrastructure as Code

| Tool | What it is | Notes |
| --- | --- | --- |
| Terraform | Declarative, multi-cloud IaC (HashiCorp), the de facto standard for GCP IaC | Google publishes/maintains an official GCP provider; most real-world and exam “IaC” answers point here |
| Config Connector | Kubernetes-native way to manage GCP resources as K8s Custom Resources (CRDs) | For teams already GitOps/K8s-centric who want GCP infra managed the same way as workloads |
| Fabric FAST | Google's opinionated, Terraform-based reference framework/blueprints for landing zones (multi-project, multi-env, secure-by-default org setup) | Accelerates org-level, best-practice foundation setup rather than being a tool from scratch |
| Helm | Kubernetes package manager — templated manifests (“charts”) for deploying apps onto GKE | Not GCP-specific (open-source K8s ecosystem tool), but explicitly named in the ACE guide for deploying to GKE |

> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Terraform ↔ Terraform (same tool, different provider) or CloudFormation/CDK. Config Connector ↔ AWS Controllers for Kubernetes (ACK) — same idea, different cloud. Fabric FAST ↔ AWS Control Tower / Landing Zone Accelerator (opinionated, best-practice foundational setup).

### AI-Assisted Planning & Implementation (new for this exam version)

| Tool | What it does |
| --- | --- |
| Gemini CLI | Terminal-based agentic AI assistant — can scaffold infra, write/explain gcloud commands, and (per Google's own migration guidance) is positioned as the evolution path for lightweight terminal-based agent work |
| Google Antigravity | An agent-first IDE/development platform (desktop app + IDE + CLI) — plans architecture, generates Infrastructure-as-Code and application code, executes and verifies deployments (e.g., can provision a Cloud Storage bucket + Pub/Sub + Cloud Run pipeline end-to-end from a natural-language brief), and connects to Google's databases (AlloyDB, BigQuery, Spanner, Cloud SQL) via MCP (Model Context Protocol) servers |
| Gemini Cloud Assist | Google Cloud's built-in AI assistant across the console — helps design architectures, generate IaC, explain errors, and (via “investigations”) perform root-cause analysis on production issues; surfaces inside Cloud Hub and Cloud Monitoring too |
| Application Design Center | A visual, AI-assisted tool for designing application architectures on Google Cloud and generating the deployable Terraform/config to match the diagram |

> [!TIP]
> 💡 **Exam Tip:** For ACE you won't need to *operate* these tools deeply — you need to recognize what each one is for if a scenario says “visually design an architecture and generate matching IaC” (Application Design Center) vs. “generate an implementation plan and write/deploy code end-to-end from a prompt” (Antigravity) vs. “get an AI explanation of a production issue in the console” (Gemini Cloud Assist).

> [!IMPORTANT]
> ⚙️ **Under the Hood:** This objective reflects a real industry shift: agentic AI is becoming a first-class part of the cloud engineer's toolchain, not just a chatbot on the side. Antigravity's MCP-based database connections and Gemini Cloud Assist's investigations both point at the same trend — AI agents that can *act* on infrastructure, not just answer questions about it. Worth exploring hands-on beyond the exam (see Part 13).

## 3.12 Domain 2 Quick-Fire Recap

| Ask yourself… | …Think |
| --- | --- |
| Containers, zero node management, pay-per-pod? | GKE Autopilot |
| Containers, need custom node/GPU/taint control? | GKE Standard |
| Simple HTTP API, scale to zero, minimal ops? | Cloud Run |
| Single event-driven function? | Cloud Run functions |
| Need global strong-consistency relational DB? | Spanner |
| Just need managed MySQL/Postgres, single region? | Cloud SQL |
| Massive-scale wide-column, high write throughput? | Bigtable |
| App backend, real-time sync, flexible schema? | Firestore |
| Petabyte-scale SQL analytics/BI? | BigQuery |
| Need a shared, centrally-governed network across many project teams? | Shared VPC |
| Need org-wide firewall rules teams can't override? | Hierarchical firewall policy (Cloud NGFW) |
| Non-HTTP TCP/UDP traffic, must preserve client IP? | External passthrough Network LB |
| Dev/test workload, cost > performance? | Standard Network Service Tier |

---

> [◀ Part 3c: Storage & Databases](./part-03c-storage-and-databases.md) | [🏠 Back to README](../README.md) | [Part 4a: Day-2 Resource Operations ▶](./part-04a-resource-management.md)
