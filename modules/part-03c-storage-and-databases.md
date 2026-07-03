# Part 3c: Storage & Databases


## 3.5 Storage & Data Products — The Master Decision Matrix

Objective 2.2 is dense: “Choosing and deploying data products.” This single table is arguably the highest-yield content in the whole handbook — know it cold.

| Product | Type | Best for | AWS analogue |
| --- | --- | --- | --- |
| Cloud SQL | Managed relational (MySQL, PostgreSQL, SQL Server) | Traditional relational apps, lift-and-shift, regional scale, up to a few TB | RDS |
| AlloyDB (for PostgreSQL) | Managed, PostgreSQL-compatible, high-performance relational | PostgreSQL apps needing higher performance/analytics (HTAP) than Cloud SQL, enterprise migrations off Oracle | Aurora PostgreSQL |
| Cloud Spanner | Globally distributed, horizontally scalable relational (SQL + strong consistency) | Mission-critical apps needing global scale + strong consistency + relational/SQL simultaneously (banking, global inventory) | No direct AWS equivalent — closest is Aurora (regional only) or DynamoDB Global Tables (not relational/strongly consistent SQL) |
| Bigtable | Wide-column NoSQL, massive scale, low-latency | Time-series, IoT, high-throughput analytics/ad-tech feeding into ML, huge write volume | DynamoDB (partial) / HBase heritage |
| Firestore | Document NoSQL, serverless, real-time sync | Mobile/web app backends, real-time listeners, flexible schema, offline sync | DynamoDB (document mode) + AppSync realtime, somewhat |
| BigQuery | Serverless data warehouse (OLAP, columnar, SQL) | Analytics at scale, BI dashboards, huge ad-hoc SQL queries over petabytes, ML via BigQuery ML | Redshift (+ Athena for serverless querying) |
| Memorystore | Managed in-memory store (Redis or Memcached, now also Valkey) | Caching, session stores, leaderboards, low-latency lookups | ElastiCache |
| Dataflow | Managed stream & batch data processing (Apache Beam) | ETL pipelines, real-time stream processing, unified batch+streaming code | Kinesis Data Analytics / Glue (partial), no exact 1:1 |
| Pub/Sub | Managed, global, at-least-once messaging (pub-sub + push/pull) | Decoupling services, event ingestion, streaming pipelines' entry point | SNS + SQS combined, or Kinesis Data Streams for streaming ingestion |
| Google Cloud Managed Service for Apache Kafka | Fully managed Apache Kafka | Teams standardized on Kafka APIs/ecosystem wanting to avoid self-hosting brokers | Amazon MSK |

### The Relational Trio: Cloud SQL vs. AlloyDB vs. Spanner

> [!TIP]
> **Exam Tip:** The single most-tested storage decision on ACE. Ask: (1) Do I need global horizontal scale + strong consistency? → Spanner. (2) Do I just need a faster/more capable PostgreSQL for a single region, maybe with heavy analytics-on-transactional-data? → AlloyDB. (3) Do I just need a standard managed MySQL/Postgres/SQL Server, single-region, moderate scale? → Cloud SQL.

### NoSQL Trio: Firestore vs. Bigtable vs. Memorystore

- Firestore: app-developer-friendly, document model, strong client SDKs (mobile/web), real-time listeners, automatic multi-region option — think “building an app's primary database quickly.”
- Bigtable: infrastructure-grade, wide-column, needs a well-designed row key to avoid hotspotting, shines at huge scale (TB–PB) and very high throughput (millions of ops/sec) — think “IoT sensor stream, ad click stream, time-series at massive scale.”
- Memorystore: not a system-of-record — it's a cache/speed layer in front of another database, or for ephemeral state like sessions/leaderboards.
> [!CAUTION]
> **Common Mistake:** Bigtable is not a good fit for small datasets (<1TB) or low-throughput use cases — it needs scale to justify its operational model. A common wrong-answer distractor is picking Bigtable for a “simple mobile app backend” (Firestore is correct there).

### BigQuery vs. Everything Else

- BigQuery is OLAP (analytical, columnar, scan-heavy aggregate queries) — not a transactional (OLTP) database. Don't use it as an app's live read/write backend.
- Serverless: no infrastructure to manage, pay per query (on-demand, bytes scanned) or via flat-rate/editions capacity-based slots for predictable heavy workloads.
- Built-in BigQuery ML lets you train/run ML models using SQL directly on warehouse data — relevant if a scenario wants “ML without moving data out of the warehouse.”
> [!NOTE]
> **AWS ↔ GCP Tip:** BigQuery's serverless, pay-per-query model has no exact RDS/Redshift equivalent (Redshift is provisioned/cluster-based by default, though Redshift Serverless narrows the gap) — Athena's serverless query-over-S3 model is the closer spiritual match, but BigQuery is a full first-class warehouse, not just a query layer over object storage.

### Pub/Sub, Dataflow, Managed Kafka — The Streaming Trio

- Pub/Sub = the ingestion/decoupling layer (many-to-many messaging). Classic pattern: producers → Pub/Sub topic → one or more subscribers (push or pull).
- Dataflow = the processing layer (transform/aggregate/enrich data in flight or in batch), built on Apache Beam — the exam expects you to know it's used for both streaming and batch with the same programming model.
- Managed Service for Apache Kafka = an alternative to Pub/Sub when an org specifically needs Kafka-API compatibility (existing Kafka producers/consumers, Kafka Connect ecosystem) rather than Pub/Sub's own API.
- Classic reference pipeline: Pub/Sub (ingest) → Dataflow (transform) → BigQuery (store & analyze). This exact 3-hop pattern shows up repeatedly across GCP exams.
> [!NOTE]
> **AWS ↔ GCP Tip:** Pub/Sub → Dataflow → BigQuery ↔ Kinesis Data Streams → Kinesis Data Analytics/Glue → Redshift. Recognize the shape even if exact product names differ.

## 3.6 Cloud Storage Options

| Storage class | Min. storage duration | Best for |
| --- | --- | --- |
| Standard | None | Frequently accessed (“hot”) data, serving content, active datasets |
| Nearline | 30 days | Accessed roughly ≤ once a month — backups, long-tail content |
| Coldline | 90 days | Accessed roughly ≤ once a quarter — disaster recovery data |
| Archive | 365 days | Accessed ≤ once a year — long-term retention, compliance/legal archives, cheapest class |

- All classes have the same low latency (milliseconds) and same 99.999999999% (11 nines) annual durability; the only differences are cost (storage price decreases, retrieval/early-deletion price increases as you go colder) and minimum storage duration (charged even if deleted sooner).
- Object Lifecycle Management (OLM): rules to auto-transition objects between classes or delete them based on age/conditions (e.g., “move to Coldline after 90 days, delete after 3 years”).
- Bucket location types: Region (single region, lowest latency/cost), Dual-region (2 specific regions, good latency + resilience), Multi-region (broad geographic area like “US”/“ASIA”, highest availability).
> [!NOTE]
> **AWS ↔ GCP Tip:** Standard↔S3 Standard, Nearline↔S3 Standard-IA, Coldline↔S3 Glacier Instant/Flexible Retrieval (roughly), Archive↔S3 Glacier Deep Archive. Key difference: all GCP storage classes have millisecond retrieval — unlike AWS Glacier Flexible/Deep Archive, which have retrieval delays (minutes to hours). This is a genuinely different mental model, not just renaming.

## 3.7 Filestore, NetApp Volumes, Managed Lustre — File Storage Trio

| Product | What it is | Best for |
| --- | --- | --- |
| Filestore | Managed NFS file storage | Lift-and-shift apps needing a traditional shared file system (e.g., legacy app servers, content management, home directories) |
| Google Cloud NetApp Volumes | Managed NetApp ONTAP-based file storage (multiprotocol: NFS + SMB) | Enterprises with existing NetApp investment/skills, need advanced data management (snapshots, replication, SMB + NFS together) |
| Google Cloud Managed Lustre | Managed high-performance parallel file system (Lustre) | HPC and large-scale AI/ML training needing extremely high-throughput, low-latency parallel file access (feeding GPU/TPU clusters) |

> [!NOTE]
> **AWS ↔ GCP Tip:** Filestore ↔ Amazon EFS. NetApp Volumes ↔ Amazon FSx for NetApp ONTAP. Managed Lustre ↔ Amazon FSx for Lustre. This trio maps almost perfectly 1:1 to the AWS FSx family — an easy set of marks if you know FSx.

## 3.8 Loading Data

- Command-line upload: gcloud storage cp (modern, recommended, replaces older gsutil cp — both still work) for direct file/object uploads.
- Load data from Cloud Storage: native “load from GCS” support in BigQuery (bq load / console), Cloud SQL import, Spanner import, etc. — GCS is the common staging area for most GCP data services.
- Storage Transfer Service: managed, scheduled, large-scale data transfer — from on-prem, another cloud (S3, Azure Blob), or between GCS buckets — with retries, verification, filtering, and scheduling built in. Not for one-off small transfers — that's just gcloud storage cp.
> [!NOTE]
> **AWS ↔ GCP Tip:** Storage Transfer Service ↔ AWS DataSync (for scheduled, large-scale, ongoing transfers) or the S3 Transfer Acceleration/Snowball family for very large one-time/offline migrations (GCP's offline equivalent is the Transfer Appliance for petabyte-scale offline moves).

## 3.9 Multi-Region Redundancy Across Data Solutions

- Cloud Storage: choose multi-region or dual-region bucket location for built-in geo-redundancy.
- BigQuery: create datasets in a multi-region location (e.g., “US”, “EU”) for automatic redundancy across regions within that area.
- Cloud SQL: not natively multi-region for the primary — use cross-region read replicas for DR/read scaling; failover requires promoting a replica (manual or scripted, not instant/automatic across regions).
- Spanner: natively supports multi-region configurations with automatic synchronous replication and 99.999% multi-region SLA — the go-to when you need write availability across regions with strong consistency.
- Firestore: offers a multi-region location option (nam5/eur3-style) with automatic replication.
> [!TIP]
> **Exam Tip:** If a scenario says “needs automatic multi-region write availability with strong consistency and zero data loss on regional failure” → Spanner (multi-region config) is almost always the intended answer over Cloud SQL replicas.

---

> [◀ Part 3b: GKE & Serverless Compute](./part-03b-gke-and-serverless.md) | [🏠 Back to README](../README.md) | [Part 3d: VPC Networking & Planning Tools ▶](./part-03d-networking-and-tooling.md)
