# Part 2b: Operations, Networking & Billing


## 2.6 Provisioning Google Cloud Observability

- “Google Cloud Observability” is the umbrella brand for Cloud Monitoring + Cloud Logging (+ Trace, Profiler, Error Reporting).
- A Monitoring Metrics Scope is created automatically per project; you can add more projects into one scope to monitor them together (useful for multi-project apps).
- First-time setup: no separate “enablement” beyond the API — Monitoring/Logging start capturing platform metrics/logs automatically once resources exist; custom application metrics/logs need the Ops Agent or client libraries.

## 2.7 Assessing Quotas & Requesting Increases

- Quotas exist per-project, per-region (some are global) to prevent runaway usage/cost and protect shared infrastructure — e.g., CPUs per region, IPs per region, API requests/min.
- View: Console → IAM & Admin → Quotas, or gcloud compute project-info describe --project=PROJECT_ID.
- Request an increase via the Console quota page (submits to Google, approval not instant — plan ahead of a launch/scale event).
- Rate quotas (e.g., API calls/min) reset automatically; allocation quotas (e.g., number of VMs) require an explicit increase request.
> [!TIP]
> **Exam Tip:** Objective 1.1 explicitly names quota assessment — expect a scenario like “your MIG can't scale beyond N instances although autoscaling policy allows more” → answer is usually hit a regional CPU/IP quota, fix = request quota increase.

## 2.8 Setting Up Standalone Organizations

- A standalone Cloud Identity org lets you create an Organization resource without a full Google Workspace subscription — ideal for a company that just wants GCP's resource hierarchy/governance, not Gmail/Docs.
- Free edition of Cloud Identity supports this; upgrade later if you want Workspace apps.

## 2.9 Setting Up Cloud Networking (Initial)

- New projects get a default VPC (auto mode, with a subnet in every region + permissive default firewall rules allowing internal traffic and SSH/RDP/ICMP) — fine for quick tests, not recommended for production (best practice: delete/avoid default VPC, use custom-mode VPCs with least-privilege firewall rules).
- Full networking depth is Part 3.10 (design) and Part 4.3 (day-2 ops).
> [!CAUTION]
> **Common Mistake:** The default VPC's default firewall rules (default-allow-internal, default-allow-ssh, default-allow-icmp, default-allow-rdp) are a favorite exam gotcha for “why can everyone SSH into my VM” style questions. Production projects should use custom-mode VPCs and explicit rules.

## 2.10 Verifying Product Availability Across Locations

- Not every machine type, GPU, or managed service exists in every region/zone — check the official “available regions/zones per product” docs or gcloud compute machine-types list --zones=... before designing an architecture.
- Common exam scenario: choosing a region for latency and confirming the required GPU/machine family is actually offered there.

## 2.11 Cloud Asset Inventory + Gemini Cloud Assist

- Cloud Asset Inventory (CAI): a historical, searchable metadata database of all your GCP resources (and their IAM policies) across projects/orgs — supports point-in-time snapshots, change history, and export to BigQuery/Cloud Storage for analysis/compliance.
- Use cases: security audits (“who had access to X on date Y”), compliance reporting, resource discovery at scale, change tracking.
- Gemini Cloud Assist (new AI layer) can now analyze CAI data conversationally — ask natural-language questions about your resource inventory, get architecture recommendations, and run root-cause investigations (also surfaced inside Cloud Hub, see Part 4.4).
> [!NOTE]
> **AWS ↔ GCP Tip:** Cloud Asset Inventory ↔ AWS Config (resource inventory + history) combined with a bit of AWS Resource Explorer (search). Gemini Cloud Assist's investigation feature is conceptually adjacent to Amazon Q Developer's operational troubleshooting features.

## 2.12 Workforce Identity Federation

- Lets external human identities (corporate Okta/Azure AD/on-prem SAML/OIDC IdP) authenticate directly to Google Cloud without creating/syncing Cloud Identity user accounts.
- Use case: contractors/partners/employees whose identity already lives in a non-Google IdP need temporary, federated access to GCP resources — no duplicate account provisioning.
- Contrast sharply with Workload Identity Federation (Part 5.2) which federates non-human workloads (apps running in AWS/Azure/on-prem/GKE) — different problem, similar-sounding name. This pairing is a guaranteed exam confusable — see Part 9.
> [!TIP]
> **Exam Tip:** Quick disambiguator to memorize: Workforce = humans/workforce from an external IdP. Workload = machines/apps/workloads running outside Google Cloud (or inside GKE) that need to call GCP APIs without a downloaded service account key.

## 2.13 Billing Configuration (Objective 1.2)

### Creating & Linking Billing Accounts

```bash
gcloud billing accounts list / gcloud billing projects link PROJECT_ID --billing-account=ACCOUNT_ID
```

- One Billing Account can fund many Projects; a Project can only be linked to one Billing Account at a time (but can be re-linked).
- Billing IAM roles: Billing Account Administrator (full control), Billing Account User (can link projects to the account — needed to attach a new project), Billing Account Viewer (read-only cost visibility).

### Budgets & Alerts

- Create Budgets scoped to a billing account, project(s), or even by label/product — set a target amount and threshold rules (e.g., alert at 50%/90%/100% of budget, or 100% of forecasted spend).
- Budget alerts notify only by default (email to billing admins, or a Pub/Sub topic for programmatic reaction) — budgets do NOT automatically stop spending or shut down resources unless you build that automation yourself (e.g., a Cloud Function triggered by the Pub/Sub budget notification that disables billing or stops VMs).
> [!CAUTION]
> **Common Mistake:** Huge, frequently-tested trap: a Billing Budget alert is just a notification, not a spending cap. If a question implies “automatically caps spend,” that's false unless it also describes a Pub/Sub-triggered automation to actually take action (e.g., disable billing / delete resources).

### Billing Exports

- Export detailed billing data to BigQuery (standard usage cost export + optional detailed/pricing export) for analysis/dashboards — the recommended, queryable option.
- File-based CSV/JSON export to Cloud Storage is also available for simpler needs.
- Standard exam pairing: “Finance wants to build custom cost dashboards and slice spend by label” → BigQuery billing export, then query/visualize (e.g., via Looker Studio).
> [!NOTE]
> **AWS ↔ GCP Tip:** Budgets & Alerts ↔ AWS Budgets. Billing Export to BigQuery ↔ AWS Cost & Usage Report (CUR) exported to S3 + queried via Athena/Redshift. GCP's version is more “native SQL warehouse” out of the box since BigQuery is the destination.

## 2.14 Domain 1 Quick-Fire Recap

| Ask yourself… | …Think |
| --- | --- |
| Need governance guardrails across many teams? | Org Policies at Folder/Org level |
| Need to grant access efficiently at scale? | Google Groups + predefined IAM roles, not individual bindings |
| Something “access denied” but role looks right? | Check if the API is enabled on the project first |
| Can't scale past N VMs even though autoscaler allows it? | Check regional quota (CPUs/IPs) |
| External partner needs temporary GCP access, no new Google account? | Workforce Identity Federation |
| Need to stop a team from blowing the budget automatically? | Budget alert (Pub/Sub) → custom Cloud Function automation — NOT automatic by itself |
| Need queryable, granular historical cost data? | Billing export to BigQuery |

---

> [◀ Part 2a: Resource Hierarchy & Access](./part-02a-resource-hierarchy-and-access.md) | [🏠 Back to README](../README.md) | [Part 3a: Compute Engine Deep Dive ▶](./part-03a-compute-engine.md)
