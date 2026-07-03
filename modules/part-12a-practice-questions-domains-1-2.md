# Part 12 — Practice Questions by Domain

42 original, exam-style scenario questions with full reasoning — written to mirror the pattern and difficulty of the real ACE exam, not copied from any dump. Cover the answer with your hand, decide, then check. Use these to test whether you've internalized the decision logic, not just facts.

## 12.1 Domain 1 — Setting Up a Cloud Solution Environment

Q1. Your company's security team wants every new project created under a specific folder to automatically block VMs from having external IP addresses, and no project owner should be able to override this. What should you do?

- A. Add a firewall rule denying all inbound traffic in each new project
- B. Set the constraints/compute.vmExternalIpAccess Organization Policy at the folder level
- C. Ask each project owner to manually avoid assigning external IPs
- D. Create a custom IAM role that omits the permission to assign external IPs

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Org Policy constraints enforce configuration guardrails that inherit down the hierarchy and cannot be overridden by IAM permissions — even a Project Owner can't violate them. IAM roles (D) control *actions*, not *configuration state*, so they can't reliably prevent this.
</details>

Q2. A developer with the Editor role reports they cannot call the Cloud Vision API from a brand-new project, receiving a permission-denied-style error. What is the most likely cause?

- A. The Editor role does not include Vision API permissions
- B. The Vision API has not been enabled on the project
- C. The developer needs the Owner role instead
- D. Cloud Vision requires a separate billing account

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. APIs must be explicitly enabled per-project before use, regardless of IAM role breadth. This is one of the most common real-world and exam “why is this broken” root causes.
</details>

Q3. Finance wants a live, queryable, granular breakdown of GCP spend by project and label, to build a custom dashboard. What should you configure?

- A. A Billing Budget with a 100% threshold alert
- B. Billing export to BigQuery
- C. The Cloud Billing Console's default cost table
- D. Cloud Asset Inventory export

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Billing export to BigQuery gives detailed, SQL-queryable cost data suitable for custom dashboards (e.g., via Looker Studio). Budgets (A) only alert; they aren't a data source for dashboards.
</details>

Q4. Your organization uses Okta as its corporate identity provider and wants contractors to access Google Cloud resources directly using their existing Okta credentials, without provisioning new Google accounts. What should you configure?

- A. Workload Identity Federation
- B. Cloud Identity user provisioning via GCDS
- C. Workforce Identity Federation
- D. A custom OAuth client in each project

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. Workforce Identity Federation federates external human identities from a corporate IdP directly, without creating Cloud Identity accounts. Workload Identity Federation (A) is for machine/workload identities, not humans.
</details>

Q5. You accidentally deleted a project 10 days ago and now need its resources back. What should you do?

- **A. Nothing can be done** — recreate everything manually
- B. Contact Google Cloud Support for an emergency restore
- C. Run gcloud projects undelete — it's still within the 30-day recovery window
- D. Restore from a Cloud Asset Inventory export

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. Deleted projects are recoverable for 30 days via gcloud projects undelete (or the Console). After 30 days, the project is permanently purged.
</details>

Q6. You want new team members to automatically receive the correct IAM roles for their function without manual per-user configuration each time someone joins or leaves. What is the best practice?

- A. Grant Owner to all engineers to avoid future requests
- B. Bind IAM roles to Google Groups representing each function, and manage group membership instead
- C. Bind IAM roles to each individual user as they join
- D. Use only the Basic Viewer role for everyone by default

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Groups-based IAM binding is the scalable best practice — onboarding/offboarding becomes a group-membership change instead of repeated IAM policy edits.
</details>

Q7. A managed instance group's autoscaler wants to scale beyond its current instance count, but new instances fail to launch across an entire region. What should you check first?

- A. The MIG's health check configuration
- B. Regional CPU or IP address quota
- C. The instance template's boot disk size
- D. The load balancer's backend service timeout

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Regional allocation quotas (CPUs, IP addresses, etc.) commonly block scale-out even when the autoscaling policy itself would allow more instances. Quota increases must be explicitly requested.
</details>

Q8. Your company wants to use GCP's resource hierarchy and IAM governance features but does not want to purchase Google Workspace. What should you do?

- A. It's impossible without Google Workspace
- B. Set up a standalone Cloud Identity organization (free edition)
- C. Use a personal Gmail account as the org root
- D. Create all projects without an Organization node

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. A free, standalone Cloud Identity edition lets you create an Organization resource and use the full resource hierarchy without a Workspace subscription.
</details>

## 12.2 Domain 2 — Planning & Implementing a Cloud Solution

Q9. A team needs to run containerized microservices, wants Google to fully manage node provisioning and patching, and wants to pay only for the CPU/memory their Pods actually request. Which should you recommend?

- A. GKE Standard with cluster autoscaler enabled
- B. Compute Engine MIG running a container-optimized image
- C. GKE Autopilot
- D. Cloud Run with a container built from each microservice

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. “Fully managed node provisioning + pay per Pod request” is the defining signal for GKE Autopilot. (D is plausible for simple stateless HTTP services but the question specifically frames this as a Kubernetes/Pod-oriented microservices need.)
</details>

Q10. You're designing a globally distributed application requiring strict transactional consistency across regions for financial transactions, with relational SQL access. Which database should you choose?

- A. Firestore in Datastore mode
- B. Cloud Bigtable with multi-cluster routing
- C. Cloud Spanner with a multi-region configuration
- D. Cloud SQL with cross-region read replicas

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. Spanner is the only option offering global horizontal scale, strong consistency, AND relational SQL simultaneously. Cloud SQL replicas (D) are eventually consistent and require manual/scripted failover — not strict multi-region consistency.
</details>

Q11. An IoT platform ingests 2 million sensor readings per second and needs low-latency time-series queries at massive scale. Which storage should you choose?

- A. Firestore
- B. Cloud Bigtable
- C. Cloud SQL for PostgreSQL
- D. Memorystore for Redis

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Bigtable is purpose-built for exactly this profile: massive write throughput, low-latency reads, time-series/IoT patterns — provided the row key is well designed to avoid hotspotting.
</details>

Q12. Compliance data must be retained for 7 years and is essentially never accessed except during an audit. What Cloud Storage class minimizes cost?

- A. Standard
- B. Nearline
- C. Coldline
- D. Archive

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** D. Archive is the cheapest class, intended for data accessed less than once a year, with a 365-day minimum storage duration — exactly matching a 7-year, audit-only retention pattern. Retrieval is still millisecond-latency, just priced higher per-access.
</details>

Q13. You need to allow multiple application teams, each in their own project, to deploy resources into a centrally managed and firewalled network that the network security team fully controls. What should you implement?

- A. VPC Network Peering between each team's project and a hub project
- B. A Shared VPC with the security team's project as the host
- C. Give each team project its own VPC with matching CIDR ranges
- D. Cloud VPN tunnels between each project

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Shared VPC is specifically designed for centralized network administration (host project) with app teams deploying into service projects using the shared network — exactly this scenario. Peering (A) doesn't centralize control the same way.
</details>

Q14. Your batch analytics job processes non-critical, fault-tolerant workloads and you want to minimize compute cost as much as possible. Which VM provisioning model should you use?

- A. Standard on-demand VMs
- B. Committed use discount VMs
- C. Spot VMs
- D. Sole-tenant nodes

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. Spot VMs offer the deepest discount (up to ~60–91%) for fault-tolerant, interruptible workloads like batch analytics.
</details>

Q15. An application needs a single global anycast IP address to serve HTTP(S) traffic to users worldwide, routing each user to the nearest healthy backend. Which load balancer should you deploy?

- A. Internal Application Load Balancer
- B. External passthrough Network Load Balancer
- C. Global external Application Load Balancer
- D. Regional external Application Load Balancer

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. “Global + single IP + HTTP(S) + nearest healthy backend worldwide” is the precise definition of the Global external Application Load Balancer.
</details>

Q16. A finance team needs to run ad-hoc SQL queries over petabytes of historical transaction data for reporting, without managing any server infrastructure. What should you recommend?

- A. Cloud SQL for PostgreSQL with read replicas
- B. BigQuery
- C. Bigtable with a SQL proxy
- D. Spanner

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. BigQuery is GCP's serverless data warehouse purpose-built for exactly this: large-scale, ad-hoc analytical SQL with zero infrastructure management.
</details>

Q17. You need an HPC workload with extremely high-throughput, low-latency parallel file access feeding a GPU training cluster. Which storage product fits best?

- A. Filestore
- B. Google Cloud NetApp Volumes
- C. Google Cloud Managed Lustre
- D. Cloud Storage FUSE

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** C. Managed Lustre is specifically built for HPC/AI-training-grade parallel file system performance — the highest throughput/lowest latency option of the three file storage products.
</details>

Q18. A developer wants an AI agent to help visually design an application architecture and automatically generate the corresponding deployable Terraform configuration. Which tool fits this need?

- A. Gemini CLI
- B. Application Design Center
- C. Cloud Asset Inventory
- D. Config Connector

<details>
<summary><b>🔍 Click to reveal Answer & Explanation</b></summary>

> **ANSWER:** B. Application Design Center is the visual, AI-assisted architecture design tool that generates matching deployable configuration — distinct from Gemini CLI (terminal-based) and Config Connector (K8s-native resource management, not visual design).
</details>

## Domain 1 & 2 Speed Drill

Cover the right column and answer each in under 10 seconds — if you hesitate, revisit that topic.

| Prompt | Answer |
| --- | --- |
| Guardrail that even an Owner can't bypass? | Organization Policy |
| Fix for “Editor role but still permission-denied on a new service”? | Enable the API |
| Cheapest storage class for yearly-access compliance data? | Archive |
| Zero node management, pay-per-pod containers? | GKE Autopilot |
| Global, strongly consistent, relational database? | Cloud Spanner |
| Massive-scale wide-column NoSQL for IoT streams? | Bigtable |
| Centralized network, app teams in separate projects? | Shared VPC |
| Deepest compute discount for fault-tolerant batch jobs? | Spot VMs |

---

> [◀ Part 11: Common Mistakes & Exam Traps](./part-11-exam-traps.md) | [🏠 Back to README](../README.md) | [Part 12b: Practice Questions (Domains 3 & 4) ▶](./part-12b-practice-questions-domains-3-4.md)
