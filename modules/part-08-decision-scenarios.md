# Part 8 — Architecture Decision Scenarios (Exam-Pattern Walkthroughs)

The ACE exam is scenario-driven. These are the recurring “shapes” of question — recognize the shape, apply the pattern. Each one follows: Situation → What to notice → Answer pattern.

## 8.1 Compute & Scaling Scenarios

| Situation | What to notice | Answer pattern |
| --- | --- | --- |
| “Re-create unresponsive VMs automatically, fewest steps” | “unresponsive” + “recreate” = health-based self-healing, not just LB routing | MIG with autohealing + health check |
| “Containerized app, team has no Kubernetes experience, minimize ops” | No K8s skill + minimize ops | GKE Autopilot (or Cloud Run if it's a simple stateless service) |
| “Batch job needs specific GPU, runs for hours, cost-sensitive, can tolerate interruption” | GPU + tolerate interruption + cost-sensitive | Compute Engine Spot VM with GPU, Terminate-on-maintenance |
| “Licensing requires specific CPU:RAM ratio not offered by standard machine types” | Odd/specific ratio | Custom machine type |
| “Need VMs to survive host maintenance with zero downtime, no special handling” | “zero downtime,” “no special handling” | Default On-host-maintenance = Migrate (live migration) — no action needed, it's default |
| “Web app receiving unpredictable traffic spikes, want to pay only for what's used, HTTP only” | HTTP + spiky + pay-for-use | Cloud Run (scale-to-zero, per-request billing) |

## 8.2 Storage & Data Scenarios

| Situation | What to notice | Answer pattern |
| --- | --- | --- |
| “Global app, needs strong consistency, relational, must survive a region outage with zero data loss” | Global + strong consistency + relational | Spanner, multi-region config |
| “Ingest millions of IoT sensor events/sec, need low-latency time-series queries” | Massive scale + time-series + high write throughput | Bigtable (with a well-designed row key) |
| “Data accessed roughly once a quarter for compliance, cost is the priority” | “once a quarter” + cost priority | Coldline storage class |
| “Grant a partner temporary download access to one file, no Google account” | Temporary + no Google account + single object | Signed URL |
| “Backup files needed for disaster recovery, following Google's recommended practice” | DR + “recommended practice” | Multi-Regional (or Nearline for infrequent access DR, depending on recovery-time need) — classic older exam answer is Multi-Regional Storage; modern equivalent is a multi-region bucket |
| “Business wants BI dashboards over petabytes of data, ad-hoc SQL” | Analytics + petabytes + SQL | BigQuery |
| “Need to reduce API calls / process a huge file upload event automatically” | Event-driven on upload | Eventarc trigger (GCS finalize event) → Cloud Run function |

## 8.3 Networking Scenarios

| Situation | What to notice | Answer pattern |
| --- | --- | --- |
| “Central security team must enforce a firewall rule no project team can override” | Org-wide, cannot override | Hierarchical firewall policy (Org/Folder level, Cloud NGFW) |
| “Instances must reach the internet for updates but must not accept inbound connections” | Outbound only, no inbound | Cloud NAT (no external IP on the instances) |
| “Host project centrally manages network; app teams deploy into their own projects” | Central network + separate app projects | Shared VPC |
| “Global HTTP(S) app, single IP, route to nearest healthy backend worldwide” | Global + HTTP(S) + single IP | Global external Application Load Balancer |
| “Non-HTTP TCP app must see the real client IP address” | Preserve client IP + non-HTTP | External passthrough Network Load Balancer |
| “Dev/test environment, minimize network cost, same-region only” | Cost priority + same-region | Standard Network Service Tier |
| “Grow a subnet without downtime because it's running out of IPs” | Expand, no downtime | Expand the subnet's primary IP range in place (cannot shrink) |

## 8.4 IAM & Security Scenarios

| Situation | What to notice | Answer pattern |
| --- | --- | --- |
| “Grant contractor access that must expire automatically on a fixed date” | Automatic expiry | IAM Condition (time-bound) on the role binding |
| “Pod must call BigQuery with least privilege, no key files” | GKE + least privilege + no keys | Workload Identity Federation for GKE (KSA → GSA mapping) |
| “CI/CD pipeline outside GCP needs to deploy without storing a long-lived key” | External CI + no stored key | Workload Identity Federation (OIDC exchange) |
| “Someone has Editor role but a specific API call still fails with permission error” | Broad role but still fails | API not enabled on the project (check first before assuming IAM issue) |
| “Need to know exactly who read a specific sensitive BigQuery table/GCS object” | Read-level audit | Ensure Data Access audit logs are enabled (off by default, except BigQuery) |
| “External employees at a partner company, already have their own IdP, need GCP access without new Google accounts” | External humans + existing IdP | Workforce Identity Federation |

## 8.5 Monitoring & Operations Scenarios

| Situation | What to notice | Answer pattern |
| --- | --- | --- |
| “Get notified only when spend crosses 90% of budget, no automatic shutdown” | Notify only | Billing Budget alert at 90% threshold (email/Pub/Sub) — no auto action unless you build it |
| “Automatically stop spending once a hard cap is hit” | Automatic action required | Budget alert → Pub/Sub → custom Cloud Run function/automation to disable billing or stop resources |
| “Correlate a production incident across logs, metrics, and recent changes quickly” | AI-assisted RCA | Gemini Cloud Assist investigation (via Cloud Hub or Monitoring) |
| “One pane of glass for health, security, cost, and support across an app spanning several projects” | Multi-project, single dashboard | Cloud Hub with an App Hub application grouping |
| “Proactively find and flag idle/oversized VMs to cut cost” | Proactive cost recommendation | Active Assist idle VM / rightsizing recommender |

---

> [◀ Part 7: Command Reference Guide](./part-07-command-reference.md) | [🏠 Back to README](../README.md) | [Part 9: Side-by-Side Concept Clarifications ▶](./part-09-confusing-concepts.md)
