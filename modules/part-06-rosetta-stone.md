# Part 6 — The AWS ↔ GCP Rosetta Stone (Master Reference Table)

Your single bookmark-and-scan reference. Organized by category. Some rows have no perfect 1:1 match — those are flagged, because assuming a false equivalence is itself a common exam trap.

## 6.1 Compute

| AWS | GCP | Notes |
| --- | --- | --- |
| EC2 | Compute Engine | Live migration by default in GCP — no AWS equivalent |
| Auto Scaling Group | Managed Instance Group (MIG) | Regional MIG spans zones; ASG is inherently multi-AZ within a region too |
| Launch Template | Instance Template | Immutable in GCP, must create a new one to change config |
| EKS (managed nodes) | GKE Standard | Full node control in both |
| EKS on Fargate | GKE Autopilot | Closest match; billing model (per-pod) is similar |
| Lambda | Cloud Run functions | Gen2 runs on Cloud Run infra |
| Fargate / App Runner | Cloud Run | Cloud Run's scale-to-zero HTTP model is closer to App Runner |
| Batch / Fargate tasks | Cloud Run jobs | Run-to-completion, not HTTP-triggered |
| Elastic Beanstalk | App Engine | Both de-emphasized/legacy-leaning in their respective ecosystems now |
| Bedrock AgentCore | Agent Runtime (Gemini Enterprise Agent Platform) | Both are managed agent hosting/runtime layers |
| Spot Instances | Spot VMs | GCP Spot: no fixed max runtime, 30-sec warning; AWS Spot: 2-min warning |
| Auto Scaling (target tracking) | MIG autoscaler / HPA (GKE) / Cloud Run autoscaling | Mechanism differs by compute type |

## 6.2 Storage

| AWS | GCP | Notes |
| --- | --- | --- |
| S3 | Cloud Storage | GCP storage classes all have ms retrieval; Glacier tiers do not |
| S3 Standard-IA / Glacier IR | Nearline | 30-day min duration |
| S3 Glacier Flexible Retrieval | Coldline | 90-day min duration |
| S3 Glacier Deep Archive | Archive | 365-day min duration |
| EBS | Persistent Disk / Hyperdisk | Regional PD has no direct EBS equivalent (cross-AZ sync block replication) |
| Instance Store | Local SSD | Both ephemeral |
| EFS | Filestore | Managed NFS |
| FSx for NetApp ONTAP | Google Cloud NetApp Volumes | Both NetApp-based |
| FSx for Lustre | Google Cloud Managed Lustre | HPC/AI parallel file systems |
| Snowball / Transfer Acceleration | Storage Transfer Service / Transfer Appliance | Transfer Appliance = offline, petabyte-scale |
| Pre-signed URL | Signed URL | Same concept |
| S3 Block Public Access | Public access prevention | Same concept |

## 6.3 Databases & Data

| AWS | GCP | Notes |
| --- | --- | --- |
| RDS | Cloud SQL | Regional managed relational |
| Aurora (PostgreSQL) | AlloyDB | Enhanced PostgreSQL-compatible performance layer |
| No exact match (Aurora Global DB is closest, but not strongly consistent across regions) | Cloud Spanner | Global, horizontally scalable, strongly consistent SQL — genuinely distinctive |
| DynamoDB | Firestore (app-dev-friendly) / Bigtable (massive-scale wide-column) | Two different GCP products cover different DynamoDB use cases |
| Redshift (+ Athena) | BigQuery | BigQuery is serverless-first by default; Redshift is cluster-first (Serverless narrows the gap) |
| ElastiCache | Memorystore | Redis/Memcached/Valkey |
| Kinesis Data Streams | Pub/Sub (+ Dataflow for processing) | Pub/Sub is pub-sub messaging, not a strict ordered stream by default |
| MSK | Google Cloud Managed Service for Apache Kafka | Fully managed Kafka in both |
| Glue / Kinesis Data Analytics | Dataflow | Apache Beam-based, unified batch+streaming |
| DMS (Database Migration Service) | Database Migration Service (same name) | Both support homogeneous/heterogeneous migration |

## 6.4 Networking

| AWS | GCP | Notes |
| --- | --- | --- |
| VPC (regional) | VPC (global) | Biggest single mindset shift |
| Subnet (1 AZ) | Subnet (regional, spans zones) |  |
| Security Group | VPC firewall rule / Cloud NGFW policy | GCP rules attach to network+Tags/SA, not ENIs |
| Network ACL | (no direct equivalent — closest: firewall rule priority ordering) | GCP firewall model is flatter than AWS's SG+NACL two-layer model |
| Direct Connect | Dedicated / Partner Interconnect | Dedicated ↔ Dedicated Connection; Partner ↔ Hosted Connection |
| Site-to-Site VPN | Cloud VPN (HA VPN for 99.99% SLA) |  |
| Transit Gateway | Network Connectivity Center | Hub-and-spoke connectivity management |
| Route 53 | Cloud DNS |  |
| NAT Gateway | Cloud NAT | GCP: no gateway object to size/place, just enable on a Cloud Router |
| ALB | Application Load Balancer (Global/Regional/Internal, L7) | GCP's Global external ALB has no exact AWS equivalent (ALB is always regional) |
| NLB | Network Load Balancer (passthrough, L4) |  |
| CloudFront | Cloud CDN (works with Cloud Load Balancing) |  |
| PrivateLink | Private Service Connect | Private, one-way consumption of a service across VPC/project boundaries |

## 6.5 IAM & Security

| AWS | GCP | Notes |
| --- | --- | --- |
| IAM Policy (attached to identity) | IAM Role binding (Role + Member on Resource) |  |
| Managed Policy | Predefined Role |  |
| Customer Managed Policy | Custom Role |  |
| Explicit Deny (always wins) | IAM Deny Policy (separate mechanism; default allow-policies don't have deny) | Key gotcha — see Part 5.1 |
| Service Control Policy (SCP) | Organization Policy | Constraint/guardrail-shaped, not permission-shaped |
| IAM Role + STS AssumeRole | Service Account + Impersonation (short-lived tokens) |  |
| Access Key / Secret Key | Service Account (JSON) Key — discouraged in both clouds | Prefer roles/impersonation over static keys in both |
| IRSA (IAM Roles for Service Accounts, EKS) | Workload Identity Federation for GKE | KSA → cloud identity mapping in both |
| IAM Identity Center / federated SSO | Workforce Identity Federation | External human IdP federation |
| KMS (SSE-KMS) | Cloud KMS (CMEK) |  |
| SSE-C | CSEK (Customer-Supplied Encryption Keys) |  |
| GuardDuty / Security Hub | Security Command Center |  |
| CloudTrail | Cloud Audit Logs (4 types: Admin Activity/Data Access/System Event/Policy Denied) | AWS splits Management vs Data events (2-way) vs GCP's 4-way split |

## 6.6 Monitoring, DevOps & Management

| AWS | GCP | Notes |
| --- | --- | --- |
| CloudWatch (Metrics/Alarms) | Cloud Monitoring |  |
| CloudWatch Logs | Cloud Logging |  |
| X-Ray | Cloud Trace |  |
| CodeGuru Profiler | Cloud Profiler |  |
| Personal Health Dashboard | Personalized Service Health |  |
| AWS Config | Cloud Asset Inventory |  |
| Trusted Advisor / Compute Optimizer | Active Assist (recommenders) |  |
| CloudFormation / CDK | Terraform (GCP provider) / Deployment Manager (legacy, being phased out) |  |
| Control Tower / Landing Zone Accelerator | Fabric FAST |  |
| Systems Manager (Patch/Inventory/State Manager) | VM Manager |  |
| Amazon Q Developer | Gemini Cloud Assist / Gemini CLI / Google Antigravity |  |
| AMP (Amazon Managed Service for Prometheus) | Google Cloud Managed Service for Prometheus |  |
| Budgets | Billing Budgets & Alerts |  |
| Cost & Usage Report (CUR) → Athena | Billing Export → BigQuery |  |

---

> [◀ Part 5: Identity, Access & Security](./part-05-access-and-security.md) | [🏠 Back to README](../README.md) | [Part 7: Command Reference Guide ▶](./part-07-command-reference.md)
