# Part 1 — GCP Fundamentals & Mental Model (For an AWS SAA Holder)

## 1.1 The Core Mindset Shift

> [!NOTE]
> **AWS ↔ GCP Tip:** You already know: shared responsibility model, regions/AZs, IAM policies, VPC/subnet/route table thinking, autoscaling, managed databases, object storage tiers. That transfers almost 1:1. What's genuinely different is below — read this once, it'll save you dozens of confused moments.

- Everything nests inside a hierarchy (Org → Folder → Project → Resource) more rigidly than AWS Organizations/OUs/Accounts — IAM and Org Policies *inherit down* this tree by default in GCP; in AWS you attach SCPs to OUs/accounts more à la carte.
- The Project is GCP's atomic billing + API + quota boundary — roughly like an AWS Account, but far more lightweight to create (you'll spin up many).
- IAM in GCP binds a role to a member on a resource (“who can do what, where”) — conceptually like AWS IAM policies, but GCP roles are collections of permissions you attach directly to identities via policy bindings, and there's no separate “resource policy” language like an S3 bucket policy for most services (Cloud Storage is an exception — it supports IAM + legacy ACLs).
- Networking is global by default. A GCP VPC is a global resource spanning all regions with regional subnets — unlike an AWS VPC, which is always confined to a single region. This is one of the single biggest mental adjustments.
- Google manages the network backbone — Google's private global fibre backbone underlies Premium Tier networking and cross-region VPC traffic, similar in spirit to AWS's use of its backbone for inter-region PrivateLink/Transit, but GCP exposes this as a selectable “Network Service Tier.”

## 1.2 GCP Global Infrastructure

- Region = independent geographic area (e.g., asia-south1 = Mumbai, us-central1 = Iowa). Each region has 3+ zones (isolated failure domains within a region, connected by low-latency links).
- Zone naming = region + letter, e.g. asia-south1-a, asia-south1-b, asia-south1-c.
- Multi-region = a named grouping (e.g. “Asia”, “US”, “EU”) used mainly by Cloud Storage and BigQuery for automatic multi-region replication.
- Not all products/machine types are available in all regions/zones — exam objective 1.1 explicitly calls out “verifying product availability across geographical locations” — use gcloud compute regions list / the region picker, don't assume.
> [!NOTE]
> **AWS ↔ GCP Tip:** Region ↔ Region, Zone ↔ Availability Zone — naming logic is nearly identical to AWS. The real difference: GCP's VPC is global (spans regions), while your AWS VPC mental model (one VPC = one region) does not carry over.

## 1.3 Resource Hierarchy

- Organization (root node, tied to a Cloud Identity/Google Workspace domain) → Folders (optional, for grouping business units/environments) → Projects (billing + resource container) → Resources (VMs, buckets, etc.)
- IAM policies and Organization Policies set at a higher node are inherited down the hierarchy; you cannot override an Org Policy to be *more* permissive lower down (you can only restrict further, unless the policy explicitly allows exceptions).
- A resource's effective IAM policy = union of policies set at every ancestor node + the resource's own policy. There is no “explicit deny” in classic IAM allow policies (deny wins only via IAM Deny Policies, a separate newer mechanism) — contrast with AWS where an explicit Deny in *any* attached policy always wins.
- Projects have 3 identifiers: Project ID (globally unique, immutable, human-chosen, used in gcloud/API calls), Project Number (globally unique, auto-assigned, immutable), Project Name (mutable, not unique, just a display label).
> [!CAUTION]
> **Common Mistake:** Exam loves testing this: you cannot reuse a deleted project's Project ID, and a deleted project stays in a recoverable ‘soft delete’ state for 30 days before permanent purge. During those 30 days you can restore it via gcloud projects undelete.

> [!NOTE]
> **AWS ↔ GCP Tip:** Org ↔ AWS Organization root. Folder ↔ roughly an OU (Organizational Unit), though GCP folders can nest arbitrarily deep vs. AWS's shallower OU trees. Project ↔ AWS Account (but far cheaper/faster to spin up — GCP encourages many small projects per team/environment; AWS encourages fewer, bigger accounts with more IAM subdivision inside).

## 1.4 Organization Policies (Org Policy Service)

- Org Policies constrain what configurations are allowed on resources (guardrails), separate from IAM (which controls who can act).
- Examples: restrict VM external IPs org-wide, restrict allowed regions for resource creation, disable service account key creation, enforce uniform bucket-level access.
- Two policy types: boolean constraints (on/off) and list constraints (allow/deny specific values, e.g., allowed regions).
- Policies inherit down the hierarchy; a child can only tighten, not loosen, unless the parent explicitly marks the constraint as “not enforced” for override.
> [!NOTE]
> **AWS ↔ GCP Tip:** Org Policy ↔ AWS Service Control Policies (SCPs). Key difference: SCPs are IAM-policy-shaped (allow/deny actions); GCP Org Policies are constraint-shaped (configuration guardrails) and are enforced *underneath* IAM — even a Project Owner cannot violate an Org Policy.

## 1.5 Ways to Interact with GCP

| Interface | What it is | AWS analogue |
| --- | --- | --- |
| Cloud Console | Web UI, console.cloud.google.com | AWS Management Console |
| Cloud Shell | Free browser-based VM with gcloud, kubectl, editor, 5GB persistent $HOME, preinstalled Cloud SDK — no local install needed | AWS CloudShell |
| `gcloud CLI` | Primary CLI for almost all GCP services (part of Cloud SDK) | AWS CLI (aws ...) |
| `gsutil / gcloud storage` | CLI for Cloud Storage (gcloud storage is the newer, recommended unified command) | aws s3 ... |
| kubectl | Kubernetes-native CLI for GKE workloads | `kubectl for EKS (same tool, K8s standard)` |
| Client Libraries / REST / RPC APIs | Programmatic access, per-service client libraries | AWS SDKs (boto3, etc.) |
| Terraform / Config Connector | Infrastructure as Code | CloudFormation / CDK / Terraform |

> [!TIP]
> **Exam Tip:** Cloud Shell comes preinstalled with the Cloud SDK (gcloud, gsutil, bq, kubectl) — exam objective 1.1 explicitly tests that you know you do not need to install anything locally to start working.

## 1.6 gcloud CLI Structure You Must Know Cold

- Pattern: gcloud <GROUP> <SUBGROUP> <ACTION> [RESOURCE] [--flags] — e.g. gcloud compute instances create my-vm --zone=asia-south1-a
- `gcloud init` — interactive first-time setup (pick account, project, default region/zone).
- `gcloud config configurations create/activate/list` — manage named configurations (multiple profiles: e.g. “work” vs “personal” project, each with its own default account/project/region).
- `gcloud config set project PROJECT_ID` — set active project for subsequent commands.
```bash
gcloud auth login (human user) vs gcloud auth activate-service-account (service account key-based) vs Application Default Credentials (gcloud auth application-default login) used by client libraries/Terraform.
```

- `gcloud components install/update` — manage optional CLI components (e.g., kubectl, beta commands).
- Almost every command supports --project=, --format=, --filter= flags for scripting.
> [!TIP]
> **Exam Tip:** A recurring exam pattern: “You're switching between an inactive GKE cluster's configuration — what's the fewest steps way to inspect it?” Know the difference between gcloud config configurations activate + gcloud config list (gcloud-level config) vs kubectl config use-context + kubectl config view (kubeconfig-level, K8s-native, works without touching gcloud config at all — often the *fewer-steps* answer when you just need cluster context info).

## 1.7 Billing Fundamentals (Preview — full detail in Part 2)

- A Billing Account pays for one or more Projects; a Project must be linked to an active Billing Account to use paid resources.
- Billing Account IAM roles are separate from Project IAM roles (e.g., Billing Account Administrator vs. Project Owner).
- $300 free trial credit (90 days) + an “always free” tier on 20+ products (e.g., limited BigQuery, Compute Engine e2-micro in specific US regions, Cloud Storage 5GB).
> [!NOTE]
> **AWS ↔ GCP Tip:** Billing Account ↔ AWS Payer/Management Account in Consolidated Billing. GCP separates “who pays” (Billing Account) from “who builds” (Project) more explicitly and lets you link/unlink projects to a billing account at any time — a lightweight version of AWS's linked-account billing relationship.

## 1.8 Mini AWS ↔ GCP Terminology Map (see Part 6 for the full Rosetta Stone)

| AWS term | GCP term |
| --- | --- |
| Account | Project |
| Organization / OU | Organization / Folder |
| IAM Policy (attached to identity) | IAM Role binding (Role bound to Member on a Resource) |
| Availability Zone | Zone |
| Region | Region |
| VPC (regional) | VPC (global, with regional subnets) |
| Security Group | Firewall rules (VPC-level, not attached to instances directly) / Cloud NGFW policy |
| EC2 Instance | Compute Engine VM instance |
| Auto Scaling Group | Managed Instance Group (MIG) |
| EBS Volume | Persistent Disk / Hyperdisk |
| S3 | Cloud Storage |
| Lambda | Cloud Run functions |
| ECS/Fargate | Cloud Run |
| EKS | GKE (Google Kubernetes Engine) |
| CloudWatch | Cloud Monitoring + Cloud Logging (Google Cloud Observability) |
| CloudTrail | Cloud Audit Logs |
| Route 53 | Cloud DNS |
| ELB (ALB/NLB) | Cloud Load Balancing (multiple types — see Part 3.10) |

---

> [◀ Part 0: Exam Blueprint & How To Use This Handbook](./part-00-exam-blueprint.md) | [🏠 Back to README](../README.md) | [Part 2a: Resource Hierarchy & Access ▶](./part-02a-resource-hierarchy-and-access.md)
