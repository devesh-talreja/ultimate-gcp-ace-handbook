# Part 13 — AWS → GCP Transition: Practical Study Plan

You're not starting from zero — AWS SAA gives you the cloud vocabulary and mental models. This plan assumes that head start.

## 13.1 Realistic Timeline

| Week | Focus | Milestone |
| --- | --- | --- |
| 1 | Fundamentals + Domain 1 (Part 1–2 of this handbook) + create free-tier GCP account, explore Console/Cloud Shell | Comfortable navigating Console + gcloud basics; resource hierarchy makes sense |
| 2 | Domain 2: Compute + Storage/Data (Part 3.1–3.9) + hands-on: Compute Engine, GKE Autopilot, Cloud Run, Cloud SQL | Deploy a simple 3-tier app manually once |
| 3 | Domain 2: Networking + tooling (Part 3.10–3.11) + hands-on: custom VPC, firewall rules, load balancer, Terraform basics | Build a custom-mode VPC with a load-balanced MIG from scratch |
| 4 | Domain 3: Operations + Monitoring/Logging (Part 4) + hands-on: set up alerting, explore Logs Explorer, try Cloud Hub | Can troubleshoot a broken deployment using Logs Explorer + Trace |
| 5 | Domain 4: IAM + Service Accounts (Part 5) + hands-on: Workload Identity Federation for GKE, custom roles, impersonation | Comfortable explaining IAM inheritance and deny-policy nuance from memory |
| 6 | Full review: Part 6–11 (Rosetta Stone, scenarios, confusables, revision notes) + take 2–3 full-length practice exams | Consistently scoring 80%+ on practice exams |

> [!TIP]
> 💡 **Exam Tip:** With an AWS SAA already in hand, 4–5 focused weeks is realistic instead of the 8–10 weeks Google suggests for complete beginners — but don't skip hands-on labs. The exam's scenario-driven style punishes “I read about it” over “I clicked through it once.”

## 13.2 Hands-On Labs That Matter Most

1. Deploy a VM with a custom machine type, attach a Hyperdisk, and test a snapshot restore.

2. Build a MIG behind a Global external Application Load Balancer, then kill an instance manually and watch autohealing recreate it.

3. Deploy the same simple app three ways: Compute Engine, GKE Autopilot, and Cloud Run — feel the operational difference firsthand.

4. Set up a custom-mode VPC with two subnets in different regions, a Cloud NAT, and firewall rules using Tags (not just IP ranges).

5. Configure Workload Identity Federation for GKE end-to-end — map a KSA to a GSA and call a real GCP API from a Pod with zero keys.

6. Build a Pub/Sub → Dataflow → BigQuery pipeline from a sample streaming source.

7. Trigger a Cloud Run function from a Cloud Storage upload via Eventarc.

8. Create a Billing Budget, then wire a Pub/Sub-triggered Cloud Run function that actually reacts to the alert (this cements the “budgets don't auto-stop spend” lesson permanently).

9. Explore Cloud Hub and Gemini Cloud Assist on a real (even if small) multi-resource project — the exam references these tools conceptually, and firsthand familiarity beats memorization.

## 13.3 Free-Tier & Cost-Control Tips for a Student Budget

- Use the $300/90-day trial credit deliberately — batch your hands-on labs into focused sessions rather than leaving resources running idle.
- Set a Billing Budget with alerts on day one (and actually wire the Pub/Sub automation — see above) so a forgotten VM doesn't burn your credit.
- Prefer e2-micro/e2-small, Cloud Run (scale-to-zero), and GKE Autopilot for lab work — they cost the least when idle.
- Always stop or delete lab VMs, GKE clusters, and Cloud SQL instances when done for the day (gcloud compute instances stop) — unlike serverless options, these bill continuously while running.
- Use Cloud Shell for quick experiments instead of spinning up a dedicated dev VM.

## 13.4 Mindset Traps Specific to AWS Veterans

- **Don't assume 1:1 naming parity** — use Part 6 (Rosetta Stone) to check, don't guess from vibes.
- Don't design VPCs “AWS-style” (one VPC per region) — lean into GCP's global VPC model.
- Don't assume IAM “deny” exists in the default allow-policy model — re-read Part 5.1 until it's automatic.
- Don't under-rate GKE Autopilot as “just EKS Fargate” and skip it — it's disproportionately tested.
- Read every question for Google's stated best practice, not just “a technically working answer” — GCP exams reward the *recommended* path, similar to AWS Well-Architected-flavored questions, but the specific recommended paths differ (e.g., predefined IAM roles, custom-mode VPC, Workload Identity Federation, Ops Agent).

---

> [◀ Part 12b: Practice Questions (Domains 3 & 4)](./part-12b-practice-questions-domains-3-4.md) | [🏠 Back to README](../README.md) | [Part 14: Career Growth & Agentic AI Trends ▶](./part-14-growth-trends.md)
