# Part 4b: Cloud Monitoring & Logging


## 4.4 Monitoring & Logging — The Biggest Sub-Objective (read this section twice)

“Google Cloud Observability” = Cloud Monitoring (metrics/alerts/dashboards) + Cloud Logging (logs) + Cloud Trace + Cloud Profiler + Error Reporting, working together.

### Cloud Monitoring: Alerts & Custom Metrics

- Alerting policies: define a condition on a metric (threshold, rate of change, absence of data) + notification channels (email, SMS, Slack, PagerDuty, Pub/Sub, webhook) — fires an incident when the condition is met, auto-resolves when it clears.
- Custom metrics: (a) log-based metrics — derive a metric by counting/extracting values from log entries matching a filter (e.g., count of ERROR-level log lines); (b) application/custom metrics — your app writes metrics directly via the Monitoring API/client libraries (or OpenTelemetry) for anything platform metrics don't cover (business KPIs, custom latencies).
- Uptime checks: synthetic, black-box probes hitting an HTTP(S)/TCP endpoint from multiple global locations — detect “is it up at all” independent of internal metrics.
> 📘 **AWS ↔ GCP Tip:** Cloud Monitoring ↔ CloudWatch (Metrics + Alarms + Dashboards). Log-based metrics ↔ CloudWatch Metric Filters on Log Groups. Uptime checks ↔ CloudWatch Synthetics / Route 53 Health Checks.

### Audit Logs — Know All Four Types

| Log type | What it captures | Enabled by default? |
| --- | --- | --- |
| Admin Activity | API calls that modify resource configuration/metadata (create/delete/update) | Yes, always on, cannot be disabled |
| Data Access | Reads/writes to the actual data within a resource (e.g., reading a BigQuery row, a GCS object read) | Mostly OFF by default (except for BigQuery Data Access logs, which are always on) — must be explicitly enabled per-service due to volume/cost |
| System Event | Google-system-initiated changes to resources (e.g., a live migration, an automatic GKE node repair) | Yes, always on, cannot be disabled |
| Policy Denied | Requests denied because of a security policy violation (e.g., VPC Service Controls, IAM Deny policy) | Yes, always on, cannot be disabled |

> ⚠️ **Common Mistake:** Data Access logs being off by default (BigQuery aside) is a very common exam trap: “we need to know who read this sensitive object/table” and the naive assumption is “it's already logged” — the correct action is usually explicitly enable Data Access audit logs for that service first.

- VPC Flow Logs: sampled records of IP-level network flows to/from VM interfaces (5-tuple, bytes/packets, latency) — enabled per-subnet, exported like any log to Logging/BigQuery/Pub/Sub. Used for network forensics, traffic analysis, cost attribution, security investigation.
- Firewall Rules Logging: per-rule toggle that logs every connection the rule evaluates (allowed or denied) — used to audit rule effectiveness/usage or debug “why is my traffic being blocked.”
> 📘 **AWS ↔ GCP Tip:** Cloud Audit Logs ↔ CloudTrail (the 4-way split into Admin Activity/Data Access/System Event/Policy Denied has no exact CloudTrail equivalent — CloudTrail just has Management vs. Data events, a 2-way split). VPC Flow Logs ↔ VPC Flow Logs (same name, same idea!). Firewall Rules Logging ↔ no exact SG equivalent in AWS (closest: VPC Flow Logs analysis, since Security Groups don't have a native per-rule hit logger).

### Log Export, Buckets, Analytics, Routing

- Log Router: the routing layer that sends log entries to destinations based on sinks (filter + destination) — destinations include Log Buckets (Cloud Logging's native storage), BigQuery, Cloud Storage, or Pub/Sub (which can then forward on-prem or to a SIEM).
- Log Buckets: where logs are actually stored within Cloud Logging — every project has a _Required bucket (Admin Activity/System Event/Access Transparency, 400-day retention, cannot be disabled/modified) and a _Default bucket (everything else, 30-day default retention, configurable, can be disabled to save cost). You can also create custom log buckets with custom retention.
- Log Analytics: lets you run SQL queries directly against a log bucket (no separate export to BigQuery required) — upgrade a log bucket to enable this.
- Exporting logs to external systems: create a sink with a Pub/Sub destination, then a subscriber (Dataflow, Cloud Run function, or a 3rd-party/on-prem SIEM connector) consumes and forwards — the standard pattern for “send our GCP logs to our on-prem Splunk/SIEM.”
> 💡 **Exam Tip:** “Send logs to an on-premises SIEM” → Log sink → Pub/Sub → (Dataflow or a forwarder) → on-prem. “Run ad-hoc SQL analysis on logs without duplicating data into BigQuery” → Log Analytics on a log bucket. “Keep logs joined with other business data long-term for BI” → export sink to BigQuery.

> 📘 **AWS ↔ GCP Tip:** Log Router + Sinks ↔ CloudWatch Logs subscription filters / Kinesis Firehose delivery. Log Buckets ↔ CloudWatch Log Groups. Log Analytics (SQL on logs in place) ↔ CloudWatch Logs Insights (query language differs — GCP's is closer to real SQL).

### Viewing & Filtering Logs

- Logs Explorer: the primary Console UI — uses a query language (resource type, log name, severity, jsonPayload.field="x", timestamp ranges) to filter; supports saving queries and jumping to a specific log entry's full detail (including structured payload, trace ID, and one-click “jump to Trace/Error Reporting” correlation).
- `gcloud logging read 'resource.type=gce_instance AND severity>=ERROR' --limit=50` — CLI equivalent for scripting/quick checks.

### Diagnostic Tools

| Tool | Purpose |
| --- | --- |
| Cloud Trace | Distributed tracing — visualize request latency across microservices/hops to find where time is spent |
| Cloud Profiler | Continuous, low-overhead code-level CPU/memory profiling in production — find which functions/lines consume resources |
| Query Insights | Cloud SQL/AlloyDB feature — visualize query performance over time, top queries by load, identify slow/expensive queries down to the query plan |
| Index Advisor | Cloud SQL/AlloyDB feature — recommends missing indexes based on observed query patterns to improve performance |

> 📘 **AWS ↔ GCP Tip:** Cloud Trace ↔ AWS X-Ray. Cloud Profiler ↔ CodeGuru Profiler. Query Insights ↔ RDS Performance Insights. Index Advisor ↔ no direct managed-RDS equivalent (closer to a DBA doing manual EXPLAIN analysis, or Redshift Advisor's recommendations for warehouse workloads) — a case where GCP bundles more DBA-assist tooling natively into the managed database experience.

### Personalized Service Health

- Shows Google Cloud service incidents/outages that are actually relevant to your projects (not the generic public status page) — filtered by the products/regions you actually use.
- Logs incidents into Cloud Logging so you can alert on them (e.g., “notify me if a GCP-side incident affects my BigQuery datasets in asia-south1”) — distinct from Cloud Monitoring alerts, which watch *your* resource metrics, not Google's own service health.
> 📘 **AWS ↔ GCP Tip:** ↔ AWS Personal Health Dashboard (PHD) — essentially the same concept and even a similar name.

### Ops Agent & Managed Service for Prometheus

- Ops Agent: the current unified agent installed on Compute Engine VMs (or on-prem/other-cloud VMs) to collect both metrics and logs in one agent — replaces the older, separate legacy Monitoring agent and Logging agent (both now deprecated). If a question mentions installing a monitoring agent on a VM, the current correct answer is Ops Agent.
```bash
gcloud compute instances ops-agents policies create to deploy fleet-wide via policy, or install manually per VM.
```

- Google Cloud Managed Service for Prometheus: fully managed, Prometheus-compatible metrics backend — lets teams keep using PromQL/Prometheus exporters/Grafana while Google manages storage/scaling/HA of the metrics backend (no self-hosted Prometheus server to run).
> 📘 **AWS ↔ GCP Tip:** Ops Agent ↔ the CloudWatch unified agent (also merged metrics+logs collection into one agent over time — same industry pattern). Managed Service for Prometheus ↔ Amazon Managed Service for Prometheus (AMP) — near-identical product, near-identical name.

### Gemini Cloud Assist for Monitoring & Active Assist

- Gemini Cloud Assist investigations: AI-driven root-cause-analysis — point it at an incident/alert and it correlates logs, metrics, traces, and recent changes to propose likely causes and next steps, surfaced in Cloud Monitoring and Cloud Hub.
- Active Assist: Google's proactive recommendation engine family — e.g., idle VM recommender (flags VMs with near-zero utilization to downsize/delete), rightsizing recommender (suggests a better machine type based on actual usage), IAM recommender (flags over-privileged roles and suggests tighter ones), firewall insights (flags shadowed/overly-permissive/unused rules), committed use discount recommender.
> 💡 **Exam Tip:** “We want proactive suggestions to right-size over-provisioned VMs and remove unused IAM permissions, with minimal manual effort” → Active Assist recommenders, not a custom-built cost/IAM audit script.

> 📘 **AWS ↔ GCP Tip:** Active Assist ↔ AWS Compute Optimizer (rightsizing) + IAM Access Analyzer (unused permissions) + Trusted Advisor, bundled into one Google-native recommendation surface across the console.

### Cloud Hub

- A centralized DevOps/SRE operations dashboard bringing together, in one place: active events (incidents + planned maintenance), health data (Monitoring alerts + metrics), security/compliance posture (from Security Command Center), capacity data (quota + Compute Engine usage), App Hub application deployment status, and open support cases — all correlatable and viewable as an interactive topology graph.
- Works at the project or App Hub application level (App Hub lets you logically group resources across projects into one “application” for unified viewing).
- This is the natural home for Gemini Cloud Assist investigations during troubleshooting — you can kick off or review an AI-driven RCA directly from Cloud Hub's Health & Troubleshooting page.
> 💡 **Exam Tip:** If a scenario describes needing one dashboard that spans health + security + cost + support + deployments for an application spanning multiple projects — that's Cloud Hub (with App Hub grouping), not a custom-built Monitoring dashboard stitched together manually.

## 4.5 Domain 3 Quick-Fire Recap

| Ask yourself… | …Think |
| --- | --- |
| Need to preserve a VM's public IP across restarts? | Reserve a static external IP |
| VM with no external IP needs outbound internet? | Cloud NAT |
| “Who read this sensitive data” and nothing was logged? | Data Access audit logs were off by default — enable them |
| Send logs to an on-prem SIEM? | Log sink → Pub/Sub → forwarder |
| Run SQL directly on logs without exporting to BigQuery? | Log Analytics on a log bucket |
| Find which code function is eating CPU in prod? | Cloud Profiler |
| Visualize latency across microservice hops? | Cloud Trace |
| Install a metrics+logs collector on a VM today? | Ops Agent (not the legacy agents) |
| Keep using PromQL/Grafana without hosting Prometheus? | Managed Service for Prometheus |
| Proactively flag idle VMs / over-privileged IAM? | Active Assist recommenders |
| One dashboard: health + security + cost + support, multi-project app? | Cloud Hub (+ App Hub) |

---

> [◀ Part 4a: Day-2 Resource Operations](./part-04a-resource-management.md) | [🏠 Back to README](../README.md) | [Part 5: Identity, Access & Security ▶](./part-05-access-and-security.md)
