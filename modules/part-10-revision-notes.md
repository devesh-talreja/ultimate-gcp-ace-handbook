# Part 10 — High-Yield Last-Minute Revision Notes

Rapid-fire facts and numbers worth memorizing cold. Read this the night before / morning of the exam.

## 10.1 Numbers to Memorize

| Fact | Number |
| --- | --- |
| Exam length / question count | 120 minutes / 50–60 questions |
| Exam validity | 2 years |
| Deleted project recovery window | 30 days (soft delete, gcloud projects undelete) |
| Cloud Storage durability (all classes) | 99.999999999% (11 nines) annually |
| Nearline minimum storage duration | 30 days |
| Coldline minimum storage duration | 90 days |
| Archive minimum storage duration | 365 days |
| Spot VM preemption notice | 30 seconds |
| Preemptible VM (legacy) max runtime | 24 hours |
| _Default log bucket retention | 30 days (configurable) |
| _Required log bucket retention | 400 days (fixed, cannot change) |
| Firestore Point-in-Time Recovery window | 7 days |
| HA VPN SLA | 99.99% |
| Spanner multi-region SLA | 99.999% |
| Always Free Cloud Storage | 5 GB-months (US regions) |
| Free trial credit | $300 (90 days) |

## 10.2 One-Line Service Identity Card

| Service | One line |
| --- | --- |
| Compute Engine | VMs, full OS control |
| GKE Autopilot | Containers, zero node management, pay-per-pod |
| GKE Standard | Containers, full node control |
| Cloud Run | Stateless HTTP containers, scale-to-zero |
| Cloud Run functions | Single-purpose event/HTTP functions |
| Cloud SQL | Managed single-region relational (MySQL/Postgres/SQL Server) |
| AlloyDB | High-performance, PostgreSQL-compatible |
| Spanner | Global, strongly-consistent, horizontally scalable relational |
| Bigtable | Massive-scale wide-column NoSQL |
| Firestore | App-friendly document NoSQL, real-time sync |
| BigQuery | Serverless SQL data warehouse (OLAP) |
| Memorystore | Managed Redis/Memcached/Valkey cache |
| Pub/Sub | Global messaging (decoupling, ingestion) |
| Dataflow | Unified batch + streaming processing (Apache Beam) |
| Cloud Storage | Object storage, 4 classes by access frequency |
| Filestore | Managed NFS |
| VPC | Global network, regional subnets |
| Cloud NAT | Outbound-only internet for private instances |
| Cloud DNS | Managed authoritative DNS |
| Cloud KMS | Key management for CMEK |
| Cloud Asset Inventory | Historical resource + IAM metadata search |
| Database Center | AI-assisted DB fleet health dashboard |
| Cloud Hub | Unified ops dashboard (health/security/cost/support) |
| Active Assist | Proactive cost/security/perf recommenders |
| Workforce Identity Federation | External human IdP → GCP access |
| Workload Identity Federation | External/GKE workload → GCP access, no keys |

## 10.3 The 10 Decisions That Show Up Most Often

1. Compute choice: full OS control vs. containers vs. serverless vs. agent hosting.

2. GKE Autopilot vs. Standard: ops overhead vs. control.

3. Cloud SQL vs. AlloyDB vs. Spanner: single-region vs. enhanced Postgres vs. global+strong-consistency.

4. Firestore vs. Bigtable: app backend vs. massive-scale infra database.

5. Storage class: access frequency → Standard/Nearline/Coldline/Archive.

6. Custom vs. Auto mode VPC: production always custom.

7. Load balancer type: HTTP(S) vs. TCP/UDP, global vs. regional, need client IP or not.

8. Predefined vs. custom IAM role: default to predefined.

9. Service account keys vs. impersonation/Workload Identity Federation: always prefer keyless.

10. Budget alert vs. automatic spend cap: alerts never auto-stop spend by themselves.

---

> [◀ Part 9: Side-by-Side Concept Clarifications](./part-09-confusing-concepts.md) | [🏠 Back to README](../README.md) | [Part 11: Common Mistakes & Exam Traps ▶](./part-11-exam-traps.md)
