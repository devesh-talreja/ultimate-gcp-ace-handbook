# Part 5 — DOMAIN 4: Configuring Access and Security (~20%)

Covers: 4.1 Managing IAM, 4.2 Managing service accounts. Dense with nuance — this domain rewards precision over breadth.

## 5.1 IAM Core Concepts

### The IAM Policy Model

- An IAM (allow) policy is a collection of bindings. Each binding = { Role: a set of permissions, Member(s): who gets it (user, group, service account, domain, or special identifiers like allUsers/allAuthenticatedUsers), optional Condition: e.g., time-bound or resource-attribute-based access }.
- Policies attach to a resource (Project, Folder, Org, or even individual resources like a single Cloud Storage bucket or Pub/Sub topic — resource-level IAM support varies by service).
- Effective policy on any resource = the union of all bindings inherited from every ancestor (Org → Folder → Project) plus bindings on the resource itself — permissions only ever add up as you go down (classic allow-policy IAM has no “explicit deny”).
- IAM Deny policies (separate, newer mechanism): explicitly deny specific permissions to specific members regardless of any allow policy — evaluated before allow policies; this is the actual mechanism that behaves like an AWS explicit Deny.
> 📘 **AWS ↔ GCP Tip:** This is one of the sharpest AWS→GCP gotchas: in AWS, an explicit Deny in any policy always wins. In classic GCP IAM (allow policies only), there's no deny — permissions purely accumulate down the hierarchy. True “deny always wins” behavior in GCP requires the separate IAM Deny policy feature, not the default allow-policy model.

### Role Types

| Role type | Examples | Guidance |
| --- | --- | --- |
| Basic roles (legacy) | Owner, Editor, Viewer | Broad, project-wide, coarse-grained — avoid in production, exam consistently marks these as anti-pattern answers for anything beyond a personal sandbox |
| Predefined roles | roles/compute.instanceAdmin.v1, roles/storage.objectViewer, roles/bigquery.dataEditor, etc. | Google-curated, service-specific, granular — the default recommended choice for most real and exam scenarios |
| Custom roles | You define an exact permission set | Use when predefined roles are either too broad or don't quite match a unique least-privilege need; can be defined at Project or Org level |

> 💡 **Exam Tip:** “Least privilege” scenario almost always resolves to: use a predefined role; only reach for a custom role when the scenario explicitly says no predefined role fits (“needs exactly these 3 permissions, no more”). Basic roles (Owner/Editor/Viewer) are essentially never the “best practice” answer once IAM is the topic.

> 📘 **AWS ↔ GCP Tip:** Basic roles ↔ AWS Managed Policies like AdministratorAccess/ReadOnlyAccess (broad). Predefined roles ↔ AWS Managed Policies scoped to a service (e.g., AmazonS3ReadOnlyAccess). Custom roles ↔ AWS Customer Managed Policies. Same philosophy, same recommended hierarchy of preference (predefined/managed first, custom only when needed).

### Policy Inheritance & the Resource Hierarchy

- Grant at the highest sensible level to reduce repetitive bindings (e.g., a Viewer role at Folder level instead of on every Project inside it) — but remember this also grants that access to everything under that node, so don't over-centralize sensitive grants.
- `gcloud projects get-iam-policy PROJECT_ID / gcloud projects add-iam-policy-binding / remove-iam-policy-binding` — core commands for viewing and editing policies.
- Policy Troubleshooter and Policy Analyzer (Console tools) help answer “why does/doesn't this member have this permission on this resource” by walking the full inherited chain — know these exist for troubleshooting scenario questions.

### IAM Conditions

- Attach a Condition (CEL expression) to a role binding to scope it further — e.g., time-bound access (“only until Dec 31”), or resource-attribute-based (“only on resources tagged env=dev”).
- Common real use: temporary elevated access for an on-call engineer or contractor that auto-expires — no manual revoke needed later.
> 📘 **AWS ↔ GCP Tip:** IAM Conditions ↔ AWS IAM policy Condition keys/blocks — same idea (attribute- or time-based scoping), different syntax (CEL vs. AWS's JSON condition operators).

## 5.2 Managing Service Accounts (heavily tested)

### What a Service Account Is

- A service account (SA) is a special kind of identity used by applications/VMs/services (not humans) to call GCP APIs — it is *both* an identity (something you grant roles to) *and* a resource (something you can grant IAM roles on, e.g., “who can act as this SA”).
- Google-managed service accounts are auto-created by certain services (e.g., the Compute Engine default SA, the App Engine default SA, the GKE default node SA) — these historically have broad Editor-like permissions by default, which is why creating your own minimally-scoped user-managed SA per workload is the recommended practice.
- Every SA has an email-like identifier: NAME@PROJECT_ID.iam.gserviceaccount.com.
> ⚠️ **Common Mistake:** Using the Compute Engine default service account (which historically has the broad roles/editor-equivalent scope) for production VMs is a textbook exam anti-pattern. Correct practice: create a purpose-built SA with only the roles that workload needs, and attach that instead.

### Using SAs in IAM Policies with Minimum Permissions

- Grant the SA only the specific predefined (or custom) roles it needs on the specific resources it touches — exactly the same least-privilege logic as human identities, just applied to a machine identity.
- Two distinct “roles about a service account” to not confuse: roles granted TO the SA (what the SA itself can do to other resources) vs. roles granted ON the SA (who/what can use or manage the SA itself — e.g., roles/iam.serviceAccountUser, roles/iam.serviceAccountTokenCreator).

### Assigning SAs to Resources

- Attach an SA to a VM at creation (--service-account= flag) — the VM's metadata server serves credentials automatically for that SA, no key file needed.
- For a human/process to launch a resource running as a given SA, they need roles/iam.serviceAccountUser on that SA (this is the classic “actAs” permission) — without it, they can't deploy a VM/Cloud Run service configured to use that SA even if they can create the resource itself.
> 💡 **Exam Tip:** “A developer can create Compute Engine instances but gets an error attaching a specific service account to the VM” → they're missing roles/iam.serviceAccountUser on that service account (separate from their instance-creation permissions).

### Service Account Impersonation & Short-Lived Credentials

- Impersonation: a human or another SA temporarily “becomes” a target SA (with roles/iam.serviceAccountTokenCreator on it) to make API calls or generate short-lived credentials — avoids ever distributing a long-lived downloadable key.
- Short-lived credentials: access tokens (typically 1-hour) or ID tokens generated on-demand via impersonation (gcloud auth print-access-token --impersonate-service-account=..., or the IAM Credentials API) — the modern, recommended alternative to downloadable JSON service account keys.
- Google strongly discourages long-lived downloadable SA keys — they don't expire by default, are easy to leak (accidentally committed to a repo), and are hard to rotate/audit. Org Policy can outright disable SA key creation org-wide (constraints/iam.disableServiceAccountKeyCreation).
> 📘 **AWS ↔ GCP Tip:** Impersonation + short-lived tokens ↔ AWS STS AssumeRole (temporary credentials) — the same underlying philosophy: prefer short-lived, assumed credentials over long-lived static access keys. GCP's SA JSON key file ↔ AWS IAM Access Key/Secret — both are the “legacy, avoid if possible” pattern in their respective clouds.

### Service Accounts in GKE: Workload Identity Federation for GKE

- Problem: Pods running on GKE nodes historically inherited the node's service account permissions by default — far too broad (any Pod on that node effectively got the node SA's access), and mounting SA key files into Pods was a leaked-credential risk.
- Workload Identity Federation for GKE (the modern, GA solution — successor to the older, now-deprecated “metadata concealment” workaround): lets a Kubernetes Service Account (KSA) be mapped to a Google Service Account (GSA), so individual Pods (via their KSA) get only the GCP permissions of the mapped GSA — fine-grained, per-workload identity, with no key file ever created or stored.
- This is the single recommended pattern the exam wants for “how should a Pod call a GCP API securely”.
> 💡 **Exam Tip:** “A Pod needs to call the BigQuery API with least privilege and no stored key files” → Workload Identity Federation for GKE: map a KSA to a purpose-built GSA holding only the needed BigQuery role.

> 📘 **AWS ↔ GCP Tip:** Workload Identity Federation for GKE ↔ IAM Roles for Service Accounts (IRSA) on EKS — functionally the same pattern: map a Kubernetes ServiceAccount to a cloud IAM identity so individual Pods get scoped, keyless cloud permissions.

### Workload Identity Federation (the broader, non-GKE version)

- Lets workloads running outside Google Cloud (on AWS, Azure, on-prem, or any OIDC/SAML-capable external identity provider) obtain short-lived GCP credentials by exchanging their own external credentials — again, no downloaded GCP SA key ever needed, even for cross-cloud workloads.
- Classic use case: a CI/CD pipeline running in GitHub Actions or on AWS needs to deploy to GCP — instead of storing a GCP SA key as a CI secret, configure Workload Identity Federation so the pipeline's own OIDC token is exchanged for a short-lived GCP token.

## 5.3 Domain 4 Quick-Fire Recap

| Ask yourself… | …Think |
| --- | --- |
| Least-privilege role for a common task? | Predefined role first, custom role only if none fit |
| Need access that truly overrides any allow-grant? | IAM Deny policy (not the default allow-policy model) |
| VM using the broad default Compute Engine SA in prod? | Anti-pattern — create a purpose-built, minimally-scoped SA |
| Dev can create a VM but can't attach a specific SA to it? | Missing roles/iam.serviceAccountUser on that SA |
| Need to call an API as an SA without a downloadable key? | Impersonation → short-lived credentials |
| Pod needs least-privilege GCP access, no key files? | Workload Identity Federation for GKE (map KSA → GSA) |
| External CI/CD pipeline (GitHub/AWS) needs to deploy to GCP? | Workload Identity Federation (non-GKE, OIDC exchange) |
| External human/contractor needs GCP access, no new Google account? | Workforce Identity Federation |

---

> [◀ Part 4b: Cloud Monitoring & Logging](./part-04b-monitoring-and-logging.md) | [🏠 Back to README](../README.md) | [Part 6: AWS ↔ GCP Rosetta Stone ▶](./part-06-rosetta-stone.md)
