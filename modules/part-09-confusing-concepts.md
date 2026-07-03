# Part 9 — Confusing & Similar Concepts, Clarified Side-by-Side

These pairs (and trios) are where test-writers concentrate difficulty — not because the concepts are individually hard, but because they sound alike or overlap. Drill this section last, right before the exam.

## 9.1 Workforce Identity Federation vs. Workload Identity Federation

|  | Workforce Identity Federation | Workload Identity Federation |
| --- | --- | --- |
| Who/what it's for | Human identities (employees, contractors, partners) | Non-human identities (apps, CI/CD pipelines, workloads outside Google Cloud, or GKE Pods) |
| External source | Corporate IdP (Okta, Azure AD/Entra ID, on-prem SAML/OIDC) | AWS/Azure workload identity, any OIDC provider, or (for GKE specifically) Kubernetes Service Accounts |
| Replaces | Manually syncing/creating Cloud Identity user accounts | Downloadable, long-lived Service Account JSON keys |
| Memory hook | “Workforce” = people who show up to work | “Workload” = the software workload/machine doing work |

## 9.2 Zonal vs. Regional Persistent Disk vs. Hyperdisk vs. Local SSD

| Type | Durability scope | Key trait |
| --- | --- | --- |
| Zonal PD | One zone | Default, cheapest, must snapshot to move zones |
| Regional PD | Two zones (synchronous) | For HA without app-level replication |
| Hyperdisk | One zone (typically) | Decoupled IOPS/throughput provisioning, newest generation |
| Local SSD | One zone, ephemeral | Fastest, but data lost on stop/terminate — never for durable data |

## 9.3 Custom Mode vs. Auto Mode VPC

|  | Auto mode | Custom mode |
| --- | --- | --- |
| Subnets | Auto-created, one per region, predefined ranges | You define region + CIDR explicitly |
| Production readiness | Not recommended — easy to run into overlaps/limits | Recommended for production |
| Exam signal | Rarely the “best practice” answer | Almost always the correct answer when “best practice”/“production” is mentioned |

## 9.4 Predefined vs. Custom vs. Basic IAM Roles

| Role type | Granularity | When it's the right exam answer |
| --- | --- | --- |
| Basic (Owner/Editor/Viewer) | Coarse, project-wide | Almost never (sandbox/demo only) |
| Predefined | Fine, service-specific | Default correct answer for “least privilege” |
| Custom | Exact, hand-picked permissions | Only when the scenario states no predefined role matches |

## 9.5 GKE Autopilot vs. GKE Standard

See the full table in Part 3.3 — quick hook: Autopilot = “I just want Pods running, Google handles nodes.” Standard = “I need to control the nodes themselves” (custom images, GPU taints, DaemonSets, privileged workloads).

## 9.6 Snapshot vs. Image

|  | Snapshot | Image |
| --- | --- | --- |
| Purpose | Backup/restore/clone a disk | Bootable template to create new VMs |
| Bootable? | No (it's disk-level backup data) | Yes |
| Typical exam phrase | “backup,” “DR,” “point-in-time” | “golden image,” “standard VM template,” “consistent deployment” |

## 9.7 Live Migration vs. Automatic Restart

|  | On-host-maintenance: Migrate | Automatic restart |
| --- | --- | --- |
| Triggered by | Google-initiated host maintenance | Any non-user-initiated termination (crash/failure) |
| Effect | VM moves to another host without stopping (near-zero downtime) | VM is restarted after it stops unexpectedly |
| Applies to GPU/Spot VMs? | No — must use Terminate instead | Yes, independently configurable |

## 9.8 Hierarchical Firewall Policy vs. Network Firewall Policy vs. VPC Firewall Rule

| Level | Scope | Who controls it |
| --- | --- | --- |
| Hierarchical firewall policy | Org or Folder — cascades to everything beneath | Central security/network team, teams below cannot override |
| Global/Regional network firewall policy (Cloud NGFW) | Applied to one or more VPCs, global or single-region | Network admins, reusable across VPCs |
| VPC firewall rule (classic) | A single VPC network | Project/network-level admins, most granular but least reusable |

## 9.9 HPA vs. VPA vs. Cluster Autoscaler vs. Node Auto-Provisioning

See the full table in Part 4.1 — quick hook: HPA = more Pods. VPA = bigger/smaller Pods. Cluster Autoscaler = more/fewer nodes in an existing pool. Node auto-provisioning = Google can create entirely new node pools automatically when nothing existing fits.

## 9.10 CMEK vs. CSEK vs. Google-Managed Encryption

| Type | Who holds the key | Effort/control tradeoff |
| --- | --- | --- |
| Google-managed (default) | Google, fully automatic | Zero effort, zero control |
| CMEK (Customer-Managed Encryption Keys) | You, in Cloud KMS | Medium effort, you control rotation/revocation |
| CSEK (Customer-Supplied Encryption Keys) | You, supplied on every API call, never stored by Google | High effort, maximum control (Google literally never holds it) |

## 9.11 Cloud Run vs. Cloud Run Functions vs. App Engine (legacy)

|  | Cloud Run | Cloud Run functions | App Engine (legacy-leaning) |
| --- | --- | --- | --- |
| Unit of deployment | Any container | A single function | An app in a supported runtime/version |
| Trigger | HTTP(S) (services) or run-to-completion (jobs) | HTTP or event (Pub/Sub, Storage, Eventarc) | HTTP |
| 2026 exam emphasis | High (named explicitly) | High (named explicitly) | Low — no longer a primary compute objective, but can still appear as a distractor |

## 9.12 Database Center vs. Cloud Hub vs. Personalized Service Health vs. Active Assist

| Tool | Focus | Scope |
| --- | --- | --- |
| Database Center | Database fleet health/security/compliance specifically | Cross-project, database-engine-specific |
| Cloud Hub | General ops: health + security + cost + support + deployments, all resource types | Project or App Hub application |
| Personalized Service Health | Google-side outages/incidents relevant to *your* resources | Project/org, Google-service-status focused |
| Active Assist | Proactive recommendations (cost, security, performance) | Cross-service recommender engine, not a dashboard |

---

> [◀ Part 8: Architecture Scenario Walkthroughs](./part-08-decision-scenarios.md) | [🏠 Back to README](../README.md) | [Part 10: High-Yield Last-Minute Revision Notes ▶](./part-10-revision-notes.md)
