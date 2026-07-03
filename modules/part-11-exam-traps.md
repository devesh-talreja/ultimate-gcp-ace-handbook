# Part 11 — Common Mistakes & Exam Traps

Patterns test-writers deliberately exploit. If you internalize these, you'll catch yourself before picking the tempting-but-wrong answer.

## 11.1 The “Sounds Right But Isn't” Traps

- “IAM Editor role should fix this permission error” — often the real blocker is the API not being enabled on the project. Always check both.
- “Just restart the VM to fix the changed IP” — ephemeral IPs change by design on stop/start; the fix is reserving a static IP, not restarting differently.
- “We have full audit logging already” — Data Access logs are OFF by default (except BigQuery). Assume they need explicit enabling unless stated otherwise.
- “A Budget will stop the spending” — Budgets only notify. Automatic spend-capping requires you to wire up a Pub/Sub-triggered action yourself.
- “Any IAM policy can explicit-deny like AWS” — classic GCP allow-policies cannot deny; only the separate IAM Deny Policy mechanism can.
- “A VPC is regional like AWS” — GCP VPCs are global; subnets are regional. Don't design multi-VPC-per-region architectures out of AWS habit.
- “Bigtable for a small app's database” — Bigtable needs scale to be the right answer; small/simple app backends point to Firestore or Cloud SQL.
- “Snapshot = bootable” — snapshots back up disk data; images are the bootable template. Don't swap these in an answer.
- “Deleting a VM keeps its disk automatically” — by default the boot disk is deleted with the VM unless auto-delete is disabled first.
- “VPC Peering is transitive” — it is not; A↔B and B↔C does not give A↔C.
- “Standard Tier works everywhere Premium does” — Standard Tier has real restrictions (e.g., generally incompatible with global load balancers).
- “Spot VMs and Preemptible VMs are the same thing” — Preemptible = legacy, 24h hard cap; Spot = current, no fixed cap, still can be reclaimed anytime with a 30-second notice.

## 11.2 Keyword → Answer Reflexes

| If you see this phrase… | …lean toward this answer |
| --- | --- |
| “fewest steps” / “least operational overhead” | The most managed/automated option (Autopilot, Cloud Run, managed recommenders) over a manual/scripted one |
| “least privilege” | Predefined IAM role scoped to the resource, or a Custom role only if none fit |
| “no downloaded keys” / “no stored credentials” | Impersonation / Workload Identity Federation |
| “Google's recommended practice” | The modern, non-legacy option (custom-mode VPC, OS Login, Ops Agent, Workload Identity, Signed URLs, etc.) |
| “global,” “worldwide,” “single IP” | Global external Application/Proxy Load Balancer, or multi-region Spanner/Storage/BigQuery |
| “cost is the top priority” | Cheapest matching option: Spot VMs, Standard Tier, Nearline/Coldline/Archive, E2 machines, GKE Autopilot for spiky small workloads |
| “centralized,” “org-wide,” “teams cannot override” | Org Policy or Hierarchical firewall policy at Org/Folder level |
| “real-time,” “mobile app,” “offline sync” | Firestore |
| “massive scale,” “time-series,” “high write throughput” | Bigtable |
| “global + strongly consistent + relational” | Spanner |

---

> [◀ Part 10: High-Yield Last-Minute Revision Notes](./part-10-revision-notes.md) | [🏠 Back to README](../README.md) | [Part 12a: Practice Questions (Domains 1 & 2) ▶](./part-12a-practice-questions-domains-1-2.md)
