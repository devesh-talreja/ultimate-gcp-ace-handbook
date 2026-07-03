# Part 4 — DOMAIN 3: Ensuring Successful Operation of a Cloud Solution (~30%)

This is “day-2 operations” — managing what's already deployed. Covers: 3.1 Compute, 3.2 Storage & data, 3.3 Networking, 3.4 Monitoring & logging (the single largest sub-objective on the whole exam).

## 4.1 Managing Compute Resources

### Connecting & Viewing Instances

- **Connect via:** Console “SSH” button (uses IAP tunneling automatically), gcloud compute ssh INSTANCE --zone=ZONE, or a plain SSH client with keys/OS Login.
- `gcloud compute instances list` — view all running instances (filter with --filter= for zone/status/labels).
- `gcloud compute instances describe INSTANCE` — full instance detail (metadata, disks, network interfaces, service account).

### Snapshots & Images

| Concept | What it is | Key facts |
| --- | --- | --- |
| Snapshot | Point-in-time, incremental backup of a Persistent Disk | Stored regionally/multi-regionally; incremental (only changed blocks after the first); used for backup/DR and cross-zone/region disk cloning |
| Image | A bootable template (OS + optionally pre-installed software) used to create new VM boot disks | Public images (Google-provided OS), custom images (your own golden images), or images created from a disk/snapshot |
| Scheduled snapshot policy | Automates recurring snapshots of a disk on a schedule with a retention policy | Attach to one or more disks; exam tests knowing this exists so you don't manually script cron-based snapshotting |

```bash
gcloud compute disks snapshot DISK --snapshot-names=NAME, gcloud compute images create, gcloud compute resource-policies create snapshot-schedule.
```

- Use a Snapshot for backup/DR/cloning a data disk. Use an Image when you need a reusable, bootable golden template to stamp out new VMs (e.g., via an instance template).
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Snapshot ↔ EBS Snapshot. Image ↔ AMI. Same conceptual split as AWS: snapshot = disk-level backup, image = bootable VM template.

### GKE Cluster & Workload Management

- `kubectl get nodes, kubectl get pods -A, kubectl get svc` — core inventory commands.
```bash
gcloud container clusters list / describe for cluster-level (not pod-level) info.
```

- **Node pools:** add (gcloud container node-pools create), edit (resize, change autoscaling bounds), remove. A node pool can have its own autoscaling range independent of other pools in the same cluster (e.g., a GPU pool scaling 0–4, a general pool scaling 3–10).
- Configuring GKE to access Artifact Registry: node service account needs roles/artifactregistry.reader (or the broader legacy Storage read role for old gcr.io-backed repos) — usually automatic with default GKE service accounts, but a common exam/real-world break point when using a custom minimal-permission node SA.
- **Kubernetes resources:** Pods (smallest deployable unit), Services (stable network endpoint + load balancing for a set of Pods — types: ClusterIP, NodePort, LoadBalancer), Deployments (manage ReplicaSets of stateless Pods, rolling updates), StatefulSets (for stateful workloads needing stable network identity + persistent storage per replica, e.g., databases/queues run in-cluster).

### Autoscaling Inside GKE (a favorite confusable set)

| Mechanism | Scales what | Trigger |
| --- | --- | --- |
| Horizontal Pod Autoscaler (HPA) | Number of Pod replicas | CPU/memory utilization or custom/external metrics |
| Vertical Pod Autoscaler (VPA) | CPU/memory requests/limits of Pods (resizes, doesn't add replicas) | Historical usage recommendations; can auto-apply (requires Pod restart) or just recommend |
| Cluster Autoscaler / Node auto-provisioning | Number of nodes in a node pool (Standard clusters) | Unschedulable Pods (scale up) / underutilized nodes (scale down) |
| GKE Autopilot Pod resource management | Both Pod sizing and node capacity, automatically | You just set Pod requests; Google right-sizes/schedules nodes behind the scenes — you don't manage HPA-vs-cluster-autoscaler tradeoffs directly, though HPA/VPA on the Pod spec still apply |

> [!CAUTION]
> ⚠️ **Common Mistake:** Don't run HPA and VPA on CPU/memory for the same workload simultaneously — they can conflict (VPA resizing while HPA is counting on stable per-pod sizing). This exact gotcha appears in scenario questions about “my autoscaling is behaving unpredictably.”

> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** HPA ↔ Kubernetes HPA on EKS (identical, it's upstream K8s). Cluster Autoscaler ↔ Cluster Autoscaler on EKS (also upstream). GKE Autopilot's combined Pod+node autoscaling ↔ Fargate's “you just set task size” model on EKS.

### Cloud Run: Deploying & Traffic Management

- Deploying a new version creates a new Revision (immutable) — gcloud run deploy SERVICE --image=IMAGE.
- **Traffic splitting:** route % of traffic across multiple revisions (canary/blue-green patterns) — gcloud run services update-traffic SERVICE --to-revisions=REV1=80,REV2=20, or route 100% to “latest” automatically.
- **Autoscaling config:** min instances (avoid cold starts, costs more — keeps warm instances), max instances (cost/capacity ceiling), concurrency (requests served per instance simultaneously).
- Same traffic-splitting/revision concepts apply to Cloud Run functions and can be mirrored in GKE via Service mesh/Ingress-level traffic splitting (e.g., using Gateway API weighted routing) — the exam may test recognizing that all three compute options support progressive rollout patterns, just via different mechanisms.
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Cloud Run Revisions + traffic splitting ↔ conceptually like Lambda Aliases + weighted routing, or App Runner/CodeDeploy canary deployments — no single perfect AWS analogue, but the canary/blue-green pattern is universal.

### AI/Agent Workload Operations

- Deploying an agent to Agent Runtime (Gemini Enterprise Agent Platform): package agent code (e.g., built with the Agent Development Kit) and deploy to the managed runtime, which handles scaling, sessions, and memory.
- **Notebooks:** Gemini Enterprise Agent Platform Workbench (managed JupyterLab, integrated with Cloud Storage/BigQuery) and BigQuery's built-in notebook experience — both let data scientists/ML engineers work interactively against GCP data without standing up their own compute.
- **Cloud Workstations:** fully managed, secure, browser-or-IDE-accessible cloud development environments (like a persistent, centrally-managed devbox) — useful when you need consistent, locked-down dev environments across a team without local setup.
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Agent Runtime ↔ Amazon Bedrock AgentCore Runtime. Workbench notebooks ↔ SageMaker Notebook Instances/Studio. Cloud Workstations ↔ AWS Cloud9 / recently, Amazon-managed dev environments in CodeCatalyst.

## 4.2 Managing Storage & Data Solutions

### Object Management & Security in Cloud Storage

- IAM (recommended) vs ACLs (legacy, object/bucket-level fine-grained): IAM is project/bucket-level and simpler to reason about at scale; ACLs give per-object grants but are harder to audit — Uniform bucket-level access disables ACLs entirely and enforces IAM-only (Google's recommended default for new buckets).
- **Signed URLs:** time-limited, cryptographically signed URLs granting temporary access to a specific object without requiring the requester to have a Google identity/IAM grant — classic use: temporary download/upload links for external users.
- **Public access prevention:** an org-policy-enforceable setting that blocks a bucket from ever being made public, regardless of IAM/ACL misconfigurations — a strong guardrail against accidental public buckets.
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Signed URL ↔ S3 Pre-signed URL (same concept, same name pattern). Uniform bucket-level access ↔ disabling ACLs / “Bucket owner enforced” setting in S3. Public access prevention ↔ S3 Block Public Access.

### Lifecycle Management, Cost, Backup/Restore

- Object Lifecycle Management rules (age, storage class, custom time, number of newer versions) — automate class transitions/deletion instead of manual cleanup.
- **Estimating storage costs:** use the GCP Pricing Calculator, or query BigQuery billing export directly for real usage-based projections — the exam wants you to know where to look, not memorize exact prices.
- **Backup/restore patterns per database:** Cloud SQL (automated backups + point-in-time recovery via binary/transaction logs, on-demand backups, cross-region backup), Firestore (managed export/import to Cloud Storage, or Point-in-Time Recovery for the last 7 days), Spanner (managed backups + point-in-time recovery within the version retention window), AlloyDB (automated + on-demand backups, continuous backups for PITR), Bigtable (backups per table, restorable to a new/same instance).

### Reviewing Job Status

- **Dataflow:** monitor pipeline jobs via the Dataflow console job graph (per-stage throughput/latency/errors) or gcloud dataflow jobs list / describe.
- **BigQuery:** monitor query/load jobs via bq show -j JOB_ID, the Console's Job History, or INFORMATION_SCHEMA.JOBS views for programmatic auditing (who ran what, bytes billed, slot usage).

### Database Center

- An AI-assisted, org-wide dashboard for database fleet health across Cloud SQL, Spanner, AlloyDB, Bigtable, Firestore (and more) — aggregates availability, security, compliance, performance, and cost signals from your projects + Security Command Center into one pane.
- Includes a Gemini-powered chat to ask natural-language questions about fleet health and get remediation recommendations; supports custom dashboard views and alerting policies on fleet-wide signals.
- **Exam signal:** any scenario about “centrally monitor database health/compliance/security posture across many projects and database engines” → Database Center, not a bespoke per-database monitoring build.
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** No single tight AWS equivalent — closest is a combination of RDS/DynamoDB/Aurora CloudWatch dashboards + AWS Config + Trusted Advisor, but Database Center's unified cross-engine single-pane view (with AI chat) is a more integrated GCP-native experience.

### Customer-Managed Encryption Keys (CMEK)

- By default, GCP encrypts data at rest with Google-managed encryption keys automatically — no config needed.
- **CMEK:** you create/control the key in Cloud KMS (Key Management Service), and the service (Cloud Storage, BigQuery, Compute Engine disks, Cloud SQL, etc.) uses your key to encrypt the data — gives you key rotation control and the ability to revoke access by disabling/destroying the key, which immediately makes the data unreadable.
- **CMEK vs. CSEK:** CMEK keys live in Cloud KMS (Google-hosted, IAM-governed); Customer-Supplied Encryption Keys (CSEK) are keys you generate and pass in on every API call — Google never stores them, higher operational burden, used only for the strictest “Google must never hold the key” requirements.
> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** CMEK ↔ AWS KMS Customer Managed Keys (SSE-KMS). CSEK ↔ SSE-C (customer-provided keys, not stored by AWS). The naming logic (Google-Managed ≈ SSE-S3, Customer-Managed ≈ SSE-KMS, Customer-Supplied ≈ SSE-C) maps almost exactly to the S3 encryption model.

## 4.3 Managing Networking Resources

- **Resizing a subnet's IPv4 range:** you can expand a subnet's primary CIDR range in place (e.g., /24 → /20) with zero downtime, as long as the new range doesn't overlap another subnet — you cannot shrink a subnet range once created.
- **Static IP addresses:** reserve external (public, regional or global depending on use) or internal static IPs so they persist across VM stop/start/recreate — by default, ephemeral IPs change when a VM is stopped/restarted. gcloud compute addresses create.
- **Custom static routes:** add routes in a VPC to direct traffic (e.g., to a NAT gateway, a next-hop VM/appliance, or on-prem via VPN/Interconnect) — every VPC has default routes for the internet gateway and subnet-local traffic; custom routes extend this.
- **Cloud DNS:** managed authoritative DNS — public zones (internet-facing) and private zones (internal-only resolution within one or more VPCs). Supports DNS forwarding, peering, and DNSSEC.
- **Cloud NAT:** gives instances without external IPs outbound internet access (e.g., for OS updates, pulling packages) without exposing them to inbound internet traffic — fully managed, no NAT gateway VM to size/patch/scale yourself.
> [!TIP]
> 💡 **Exam Tip:** Static IP that changes on VM restart is a classic “why did my DNS/whitelist break” trap — the fix is always reserve a static (external or internal) address and assign it, not “just restart it correctly.”

> [!NOTE]
> 📘 **AWS ↔ GCP Tip:** Cloud DNS ↔ Route 53. Cloud NAT ↔ AWS NAT Gateway — near-identical purpose, but Cloud NAT requires zero provisioning of a gateway instance/resource sitting in a subnet (it's not an object you place in your topology the way a NAT Gateway is); you just enable it on a Cloud Router. Static IP behavior (ephemeral-by-default, must reserve to persist) is the same mental model as an AWS EIP not being auto-attached to a stopped/relaunched instance.

- Managing firewall rules / Cloud NGFW policies day-2: adding/removing/re-prioritizing rules, updating Tags/service-account targets, and auditing rule hit counts via Firewall Rules Logging (see 4.4) to find unused or overly-permissive rules.

---

> [◀ Part 3d: VPC Networking & Planning Tools](./part-03d-networking-and-tooling.md) | [🏠 Back to README](../README.md) | [Part 4b: Cloud Monitoring & Logging ▶](./part-04b-monitoring-and-logging.md)
