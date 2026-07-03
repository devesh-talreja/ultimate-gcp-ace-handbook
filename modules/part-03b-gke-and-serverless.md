# Part 3b: GKE & Serverless Compute


## 3.3 GKE (Google Kubernetes Engine) — Deep Dive

### Autopilot vs. Standard (guaranteed exam topic)

| Aspect | GKE Autopilot | GKE Standard |
| --- | --- | --- |
| Node management | Fully managed by Google — no node pools to configure/patch | You create & manage node pools (machine type, size, autoscaling) |
| Billing | Per Pod resource requests (vCPU/memory/storage actually requested) | Per underlying VM (node), regardless of Pod packing efficiency |
| Control/customization | Restricted — hardened, opinionated defaults (e.g., some privileged workloads/DaemonSets limited) | Full control — custom node images, DaemonSets, privileged pods, GPU node pools, taints/tolerations, etc. |
| Ops overhead | Minimal — great for teams without dedicated K8s ops | Higher — you own upgrades, node sizing, bin-packing efficiency |
| Best exam signal words | “minimize operational overhead,” “no node management,” “pay only for what pods use” | “fine-grained control,” “custom node config,” “specific GPU/node taints,” “DaemonSets” |

> 📘 **AWS ↔ GCP Tip:** GKE Autopilot ↔ EKS on Fargate (no node/EC2 management, pay-per-pod-ish). GKE Standard ↔ EKS with self-managed or managed EC2 node groups (you control instance types/sizing).

### Cluster Configurations

- Zonal cluster: control plane in a single zone — cheaper, less resilient (control plane outage = can't manage cluster, though running workloads keep running).
- Regional cluster: control plane replicated across multiple zones in a region — higher availability, recommended for production.
- Private cluster: nodes have no public IPs; can also make the control plane endpoint private (accessible only from authorized networks/VPN/Interconnect). Public clusters expose the control plane endpoint publicly (still IAM/RBAC gated) and optionally give nodes public IPs.
- Node pools: a group of nodes within a cluster sharing the same config (machine type, disk, labels, taints) — a cluster can have multiple node pools (e.g., a GPU pool + a general pool).
> 💡 **Exam Tip:** “Security team requires nodes have no exposure to the public internet” → Private cluster. “We need the control plane to survive a single zone outage” → Regional cluster. These two dimensions (public/private × zonal/regional) are independent — don't conflate them.

### kubectl, Deploying Apps, Artifact Registry

- `gcloud container clusters get-credentials CLUSTER --zone/--region` — fetches cluster credentials into your local kubeconfig, letting kubectl talk to the right cluster.
- Artifact Registry (successor to the older Container Registry/gcr.io) stores container images, language packages (npm, Maven, Python), and OS packages; GKE nodes need the right IAM (roles/artifactregistry.reader, typically via the node's service account) to pull images.
- Standard flow: build image → push to Artifact Registry → kubectl apply -f deployment.yaml referencing that image → GKE schedules Pods.
> 📘 **AWS ↔ GCP Tip:** Artifact Registry ↔ Amazon ECR (+ CodeArtifact for language packages, combined into one GCP product). get-credentials ↔ aws eks update-kubeconfig.

## 3.4 Serverless Compute — Cloud Run & Cloud Run Functions

- Cloud Run (services): long-running, HTTP(S)-triggered containers, auto-scale (including to zero), you set concurrency (requests per container instance), CPU/memory, min/max instances.
- Cloud Run (jobs): run-to-completion tasks (not HTTP-triggered) — batch processing, data migrations, scheduled tasks (often triggered by Cloud Scheduler).
- Cloud Run functions (formerly Cloud Functions): single-purpose functions, two flavors — HTTP-triggered or event-triggered (Pub/Sub messages, Cloud Storage object changes, and broadly via Eventarc for many other Google Cloud/3rd-party event sources).
- Under the hood, Cloud Run functions (2nd gen) now runs on the same infrastructure as Cloud Run — giving it Cloud Run's concurrency, longer timeouts, and larger instance sizes vs. the older 1st-gen model.

### Eventarc & Event-Driven Patterns

- Eventarc = the unified eventing backbone: routes events from 100+ Google Cloud sources (Cloud Storage, Pub/Sub, Firestore, Cloud Audit Logs, etc.) and custom/3rd-party sources (via CloudEvents) to a destination (Cloud Run, Cloud Run functions, GKE, Workflows).
- Cloud Storage object change notifications: fire on object finalize/delete/archive/metadataUpdate — route via Eventarc (recommended, modern path) or the older direct Pub/Sub notification config.
- Classic event-driven pipeline pattern (exam favorite): Cloud Storage upload → Eventarc trigger → Cloud Run function processes file → writes result to BigQuery/Firestore.
> 📘 **AWS ↔ GCP Tip:** Cloud Run ↔ AWS Fargate/App Runner. Cloud Run functions ↔ AWS Lambda. Eventarc ↔ Amazon EventBridge (unified eventing across AWS services + custom sources) — very close conceptual match.

### GPUs & TPUs — When to Use Which

| Accelerator | Best for | Notes |
| --- | --- | --- |
| GPU (NVIDIA, via A/G-series VMs, GKE node pools, or Cloud Run) | General-purpose ML training/inference, graphics, HPC, flexible framework support (PyTorch/TensorFlow/JAX/CUDA workloads) | Wider software ecosystem compatibility; can attach to Compute Engine, GKE nodes, or (newer) directly to Cloud Run services |
| TPU (Tensor Processing Unit, Google-designed ASIC) | Large-scale deep learning training/inference, especially TensorFlow/JAX at big batch sizes | Best cost/perf at large-scale, Google-proprietary; less flexible for non-ML or non-supported-framework workloads |

> 💡 **Exam Tip:** Exam-level heuristic: general/flexible ML or non-ML acceleration → GPU. Massive-scale, cost-sensitive deep learning training on supported frameworks → TPU. You won't need deep ML expertise — just this selection logic plus “GPUs require Terminate (not Migrate) as the host maintenance policy.”

---

> [◀ Part 3a: Compute Engine Deep Dive](./part-03a-compute-engine.md) | [🏠 Back to README](../README.md) | [Part 3c: Storage & Databases ▶](./part-03c-storage-and-databases.md)
