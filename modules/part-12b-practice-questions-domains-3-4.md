# Part 12b: Practice Questions (Domains 3 & 4)


## 12.3 Domain 3 — Ensuring Successful Operation of a Cloud Solution

Q19. Instances in a managed instance group occasionally become unresponsive but stay in a “running” state. You need them automatically recreated with the fewest configuration steps. What should you configure?

- A. A load balancer health check only
- B. MIG autohealing with a dedicated health check
- C. A Cloud Monitoring alert that pages an on-call engineer
- D. Cluster Autoscaler
ANSWER:  B. Autohealing is the MIG feature specifically responsible for recreating unresponsive instances, driven by its own health check — independent of any load balancer health check, which only affects traffic routing.

Q20. Security wants to know exactly which users read a specific sensitive object in a Cloud Storage bucket over the last month, but a review shows no such records exist. What is the most likely explanation and fix?

- A. Cloud Storage doesn't support this kind of logging at all
- B. Data Access audit logs were not enabled for this service — enable them going forward
- C. VPC Flow Logs need to be enabled instead
- D. The bucket needs Object Versioning turned on
ANSWER:  B. Data Access audit logs (which capture object reads) are off by default for most services, unlike Admin Activity, System Event, and Policy Denied logs, which are always on. They must be explicitly enabled.

Q21. Your company's compliance team requires all logs to be forwarded to an on-premises SIEM in near real time. What is the recommended GCP pattern?

- A. Manually export logs daily via the Console
- B. Create a log sink to Pub/Sub, then consume with a forwarder (e.g., Dataflow) to the on-prem SIEM
- C. Enable Log Analytics and query it from the SIEM directly
- D. Export logs to a Cloud Storage bucket and poll it hourly from on-prem
ANSWER:  B. Log sink → Pub/Sub → forwarder (Dataflow or similar) is the standard, near-real-time pattern for streaming GCP logs to an external SIEM.

Q22. A VM that previously had a public IP address changes its address every time it's stopped and restarted, breaking a partner's IP allowlist. What is the fix?

- A. Enable OS Login on the instance
- B. Reserve and assign a static external IP address
- C. Move the VM to a regional Persistent Disk
- D. Enable live migration on the instance
ANSWER:  B. External IPs are ephemeral by default and change on stop/start unless a static IP is explicitly reserved and assigned.

Q23. You want to identify which code function is consuming excessive CPU in a production service without adding manual instrumentation everywhere. What tool should you use?

- A. Cloud Trace
- B. Cloud Profiler
- C. Cloud Monitoring uptime checks
- D. Query Insights
ANSWER:  B. Cloud Profiler provides continuous, low-overhead, code-level CPU/memory profiling in production — exactly for “which function is eating resources” questions. Cloud Trace (A) shows latency across service hops, not per-function CPU.

Q24. Your team wants a single dashboard showing incident health, security posture, cost, and open support cases for an application that spans three separate GCP projects. What should you set up?

- A. Three separate Cloud Monitoring dashboards, one per project
- B. Cloud Hub with an App Hub application grouping the three projects
- C. Personalized Service Health for each project
- D. A custom BigQuery dashboard built from exported logs
ANSWER:  B. Cloud Hub, combined with an App Hub application (which logically groups resources across multiple projects), is purpose-built for this unified, cross-project operational view.

Q25. A Compute Engine fleet has grown organically and finance suspects many VMs are oversized or idle. What is the most efficient way to identify optimization opportunities?

- A. Manually review CPU graphs for every VM in Cloud Monitoring
- B. Use Active Assist's idle VM and rightsizing recommenders
- C. Export billing data and build a custom cost model from scratch
- D. Migrate all VMs to Spot pricing immediately
ANSWER:  B. Active Assist proactively surfaces idle-VM and rightsizing recommendations without manual review — the efficient, Google-native answer.

Q26. You installed a monitoring agent on your Compute Engine VMs several years ago and now need to collect both metrics and logs with a single, currently supported agent. What should you install?

- A. The legacy Cloud Monitoring agent
- B. The legacy Cloud Logging agent
- C. Ops Agent
- D. Cloud Trace agent
ANSWER:  C. Ops Agent is the current, unified agent for both metrics and logs, replacing the deprecated separate legacy Monitoring and Logging agents.

## 12.4 Domain 4 — Configuring Access and Security

Q27. A Pod running on GKE needs to call the BigQuery API with least-privilege access, and your security policy prohibits any downloadable service account keys inside containers. What should you configure?

- A. Mount a service account JSON key as a Kubernetes Secret
- B. Grant the GKE node's default service account the BigQuery Admin role
- C. Configure Workload Identity Federation for GKE, mapping a Kubernetes Service Account to a purpose-built Google Service Account
- D. Use the cluster's Compute Engine default service account for all Pods
ANSWER:  C. Workload Identity Federation for GKE is the modern, keyless, least-privilege pattern — mapping a KSA to a GSA scoped to only the needed BigQuery role.

Q28. A developer has permission to create Compute Engine instances but receives an error when trying to attach a specific, purpose-built service account to a new VM. What permission are they most likely missing?

- A. roles/compute.instanceAdmin.v1
- B. roles/iam.serviceAccountUser on that specific service account
- C. roles/owner on the project
- D. roles/iam.serviceAccountKeyAdmin
ANSWER:  B. Attaching (“actAs”-ing) a service account to a resource requires roles/iam.serviceAccountUser granted on that service account, separate from general instance-creation permissions.

Q29. You need to grant a contractor temporary access to a project that must automatically expire on a specific date without manual follow-up. What IAM feature should you use?

- A. A custom role with a built-in expiration field
- B. An IAM Condition with a time-based expression on the role binding
- C. A scheduled Cloud Function that removes the binding later
- D. Basic Viewer role, which auto-expires after 90 days
ANSWER:  B. IAM Conditions support time-bound expressions directly on a role binding, so access expires automatically without needing separate automation (though C could work, it's unnecessary extra complexity when Conditions solve it natively).

Q30. Your organization wants to eliminate long-lived, downloadable service account keys across all projects to reduce credential leak risk. What should you configure?

- A. Rotate all keys weekly instead of disabling them
- B. Set the constraints/iam.disableServiceAccountKeyCreation Organization Policy
- C. Store all keys in Secret Manager instead of files
- D. Require MFA for all service accounts
ANSWER:  B. This Org Policy constraint prevents new service account keys from being created org-wide, forcing teams toward impersonation/Workload Identity Federation patterns instead.

Q31. A CI/CD pipeline running in GitHub Actions needs to deploy resources to GCP. Security requires no long-lived GCP credentials be stored as a GitHub secret. What should you configure?

- A. A service account key stored as an encrypted GitHub secret
- B. Workload Identity Federation, exchanging GitHub's OIDC token for short-lived GCP credentials
- C. Basic auth using a Cloud Identity user's password
- D. A firewall rule allowlisting GitHub's IP ranges
ANSWER:  B. Workload Identity Federation lets external workloads (like a GitHub Actions pipeline) exchange their own OIDC token for short-lived GCP credentials, with zero long-lived keys stored anywhere.

Q32. A predefined IAM role grants slightly more access than a workload strictly needs, and no combination of predefined roles matches exactly. What should you do?

- A. Grant the closest predefined role and accept the extra access
- B. Create a Custom role with exactly the required permissions
- C. Grant the Basic Editor role instead
- D. Use IAM Conditions to fully replace the role assignment
ANSWER:  B. This is precisely the scenario where a Custom role is the right call — when no predefined role matches the exact least-privilege need.

Q33. External auditors from a partner firm need Google Cloud Console access for two weeks, and your company does not want to create Google Workspace accounts for them. Auditors already authenticate via their firm's Azure AD. What is the best solution?

- A. Create temporary Gmail accounts and grant IAM roles to them
- B. Configure Workforce Identity Federation with Azure AD as the identity provider
- C. Share a single service account key with the audit team
- D. Configure Workload Identity Federation with Azure AD
ANSWER:  B. External human auditors federating from a corporate IdP (Azure AD) is the textbook Workforce Identity Federation use case — not Workload Identity Federation, which is for machine/workload identities.

Q34. A security review finds a project's Compute Engine VMs are all using the project's default service account, which has broad Editor-equivalent access. What is the recommended remediation?

- A. Leave it as-is since it simplifies management
- B. Create purpose-built service accounts with only the roles each VM's workload needs, and reattach them
- C. Delete the default service account entirely with no replacement
- D. Grant the Owner role instead, since it's more explicit
ANSWER:  B. Replacing broad default service accounts with minimally-scoped, purpose-built ones is the standard least-privilege remediation.

## 12.5 AWS-Lens Trap Questions (Specifically for AWS-Background Candidates)

These are written to catch the exact assumptions an AWS SAA holder is likely to bring in — read carefully, the “obvious” AWS-style answer is usually wrong here.

Q35. You create a VPC in asia-south1 intending to also deploy resources in us-central1 within the same network. What must you do?

- A. Create a second VPC in us-central1 and peer it to the first
- B. Nothing extra for the VPC itself — just add a new subnet in us-central1 to the existing VPC, since GCP VPCs are global
- C. Enable a “multi-region” flag on the VPC
- D. Use VPC Network Peering within the same project
ANSWER:  B. Unlike AWS (where a VPC is regional and you'd need a second VPC + peering/Transit Gateway), a GCP VPC is global by default — you simply add a regional subnet in the new region to the same VPC.

Q36. A project's IAM policy grants a group roles/viewer at the Organization level, but a specific project also has an explicit binding denying that same group access via a standard IAM policy binding with no role. Does the group have Viewer access on that project?

- A. No — explicit denies in any IAM policy always override allows, just like AWS
- B. Yes — classic GCP allow policies have no “deny,” permissions only accumulate down the hierarchy unless an actual IAM Deny Policy is used
- C. It depends on which policy was created first
- D. Access is undefined and must be tested manually
ANSWER:  B. This is the single sharpest AWS→GCP gotcha: standard GCP allow-policy bindings cannot “deny” — you cannot remove inherited access by simply not granting it lower down. True deny-wins-always behavior requires the separate IAM Deny Policy feature.

Q37. You configure a Billing Budget at $10,000/month with a 100% threshold alert. Spend reaches $10,500. What happens to running resources?

- A. GCP automatically stops all billable resources in the project
- B. GCP automatically caps future spend at $10,000 for the rest of the month
- C. Nothing happens to resources automatically — only a notification is sent unless you built custom automation
- D. The project is automatically suspended
ANSWER:  C. Unlike some AWS cost-control automations people assume exist, GCP Budgets are notification-only by default. Any automatic action requires you to build it (e.g., a Pub/Sub-triggered function).

Q38. Your team is used to attaching an EBS volume across multiple AZs via Multi-Attach for shared access. What is the closest GCP mechanism for synchronous cross-zone block storage redundancy?

- A. Local SSD with RAID 1
- B. Regional Persistent Disk
- C. Cloud Storage FUSE
- D. Zonal Persistent Disk with manual replication scripts
ANSWER:  B. Regional Persistent Disk provides synchronous replication across two zones natively — GCP's answer to needing block-storage resilience beyond a single zone, without needing app-level replication.

Q39. In AWS you're used to Security Groups being attached directly to an ENI/instance. How do GCP VPC firewall rules primarily determine which instances they apply to?

- A. They must be attached directly to each instance's network interface
- B. They apply network-wide by default, scoped down via Tags or service accounts as targets
- C. They only apply per-subnet
- D. They require a separate Network ACL layer to take effect
ANSWER:  B. GCP firewall rules are defined at the VPC network level and scoped to specific instances using Tags or service accounts as targets — not attached per-ENI like an AWS Security Group.

Q40. You assume Cloud Storage Coldline behaves like S3 Glacier Flexible Retrieval, where retrieval can take minutes to hours. Is this assumption correct?

- A. Yes, Coldline retrieval typically takes several hours
- B. No — Coldline (like all GCP storage classes) offers millisecond retrieval latency; only the storage/retrieval pricing and minimum duration change between classes
- C. Yes, but only for objects larger than 1GB
- D. No, Coldline requires a manual restore request like Glacier
ANSWER:  B. This is a genuine architectural difference, not just renaming — every GCP storage class has the same millisecond latency; you're only trading off cost and minimum storage duration, never retrieval speed.

Q41. You expect to need a NAT Gateway resource sized and placed in a specific subnet, similar to AWS. What is different about Cloud NAT?

- A. Cloud NAT works identically — you place and size a gateway instance in a subnet
- B. Cloud NAT is a fully managed configuration on a Cloud Router — there's no gateway instance to size, place, or scale yourself
- C. Cloud NAT only works for a single VM at a time
- D. Cloud NAT requires a bastion host in front of it
ANSWER:  B. Cloud NAT has no visible gateway resource to provision or scale — you enable it as configuration on a Cloud Router, and Google handles all underlying capacity.

Q42. You're used to EC2 Spot Instances giving a 2-minute interruption warning. How much warning do GCP Spot VMs give before preemption?

- A. 2 minutes, same as AWS
- B. 30 seconds
- C. 5 minutes
- D. No warning at all
ANSWER:  B. GCP Spot VMs give a 30-second termination notice — shorter than AWS's 2-minute Spot warning. Design shutdown handlers accordingly if migrating Spot-tolerant workloads from AWS.

---

> [◀ Part 12a: Practice Questions (Domains 1 & 2)](./part-12a-practice-questions-domains-1-2.md) | [🏠 Back to README](../README.md) | [Part 13: AWS-to-GCP Study Plan ▶](./part-13-study-plan.md)
