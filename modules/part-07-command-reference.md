# Part 7 — gcloud & kubectl Command Reference (High-Frequency Commands)

## 7.1 Setup & Config

| Command | What it does |
| --- | --- |
| `gcloud init` | Interactive setup — account, project, default region/zone |
| `gcloud config set project PROJECT_ID` | Set active project |
| `gcloud config set compute/region REGION` | Set default region |
| `gcloud config configurations create NAME` | New named config profile |
| `gcloud config configurations activate NAME` | Switch active profile |
| `gcloud auth login` | Human user login |
| `gcloud auth activate-service-account --key-file=` | Auth as SA via key (legacy pattern) |
| `gcloud auth application-default login` | Set Application Default Credentials for SDKs/Terraform |
| `gcloud services enable SERVICE.googleapis.com` | Enable an API on the project |
| `gcloud projects list / create / describe` | Project management |

## 7.2 Compute Engine

| Command | What it does |
| --- | --- |
| `gcloud compute instances create NAME --zone=` | Create a VM |
| `gcloud compute instances list` | List instances |
| `gcloud compute ssh NAME --zone=` | SSH into a VM (via IAP if configured) |
| `gcloud compute disks snapshot DISK` | Create a snapshot |
| `gcloud compute images create` | Create a custom image |
| `gcloud compute instance-templates create` | Create an instance template |
| `gcloud compute instance-groups managed create` | Create a MIG |
| `gcloud compute instance-groups managed set-autoscaling` | Configure MIG autoscaling |
| `gcloud compute addresses create` | Reserve a static IP |
| `gcloud compute firewall-rules create` | Create a VPC firewall rule |

## 7.3 GKE / kubectl

| Command | What it does |
| --- | --- |
| `gcloud container clusters create NAME` | Create a GKE Standard cluster |
| `gcloud container clusters create-auto NAME` | Create a GKE Autopilot cluster |
| `gcloud container clusters get-credentials NAME` | Fetch kubeconfig credentials |
| `gcloud container node-pools create` | Add a node pool |
| `kubectl get pods / nodes / svc -A` | Inventory workloads/nodes/services |
| `kubectl apply -f file.yaml` | Deploy/update a resource |
| `kubectl config get-contexts / use-context` | Inspect/switch kubeconfig context |
| `kubectl logs POD` | View pod logs |
| `kubectl scale deployment NAME --replicas=N` | Manual scale |
| `kubectl autoscale deployment NAME --min= --max= --cpu-percent=` | Configure HPA |

## 7.4 Storage, Data, Networking, IAM

| Command | What it does |
| --- | --- |
| `gcloud storage cp / gcloud storage rsync` | Copy/sync objects to/from Cloud Storage |
| `gcloud storage buckets create` | Create a bucket |
| `bq load / bq query` | Load data into / query BigQuery |
| `gcloud sql instances create / backups create` | Cloud SQL instance & backup management |
| `gcloud compute networks subnets create --range=` | Create a custom-mode subnet |
| `gcloud compute networks subnets expand-ip-range` | Grow a subnet's CIDR |
| `gcloud compute routers nats create` | Configure Cloud NAT (on a Cloud Router) |
| `gcloud dns record-sets create` | Add a DNS record |
| `gcloud projects add-iam-policy-binding` | Grant an IAM role |
| `gcloud iam service-accounts create` | Create a service account |
| `gcloud iam service-accounts add-iam-policy-binding` | Grant access ON a service account (e.g., serviceAccountUser) |
| `gcloud auth print-access-token --impersonate-service-account=` | Get a short-lived token via impersonation |
| `gcloud logging read` | Query logs from the CLI |
| `gcloud monitoring channels / policies create` | Manage alerting |

---

> [◀ Part 6: AWS ↔ GCP Rosetta Stone](./part-06-rosetta-stone.md) | [🏠 Back to README](../README.md) | [Part 8: Architecture Scenario Walkthroughs ▶](./part-08-decision-scenarios.md)
