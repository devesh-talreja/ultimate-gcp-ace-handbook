# Part 0: Exam Blueprint & How To Use This Handbook

Welcome to the **Ultimate Google Cloud Associate Cloud Engineer (ACE) Handbook & Cheatsheet**. This study guide is fully aligned with the official exam guide for exams taken **on or after June 30, 2026**. 

This handbook is designed as a high-yield study companion. It is specifically optimized to help engineers—particularly those transitioning from an AWS background—rapidly map concepts, master GKE operations, memorize essential CLI syntax, and avoid common exam traps.

---

## 0.1 Exam Snapshot (Standard Exam, valid on/after June 30, 2026)

| Attribute | Value |
| --- | --- |
| **Exam Name** | Google Cloud Certified – Associate Cloud Engineer (ACE) |
| **Format** | 50–60 questions — multiple choice + multiple select (no labs, no typing) |
| **Duration** | 120 minutes |
| **Delivery** | Remote proctored (OnVUE) or physical testing center |
| **Cost** | $125 USD (retakes: $125, renewal: $75) |
| **Passing Score** | Not officially published (estimated ~70%). Pass/Fail shown immediately; official results via email in 7–10 business days |
| **Validity** | 2 years |
| **Languages** | English, Japanese, Spanish, Portuguese |
| **Prerequisites** | None formal (Google recommends 6+ months of hands-on GCP experience) |
| **Retake Policy** | 14-day wait after 1st fail, 60-day after 2nd, 365-day after 3rd+ |

> 💡 **Exam Tip:** This is a scenario-driven exam — most questions describe a business/technical situation and ask “what should you do?” rather than testing raw memorization. Practice thinking in terms of trade-offs (cost vs. ops overhead vs. control), not just facts.

---

## 0.2 Domain Weightings (official, post–June 30 2026 guide)

| Domain | Weight | Core Focus |
| --- | --- | --- |
| **1. Setting up a cloud solution environment** | ~20% | Resource hierarchy, IAM basics, billing, project setup, Cloud Identity |
| **2. Planning & implementing a cloud solution** | ~30% | Compute/storage/data/networking design & deployment choices |
| **3. Ensuring successful operation of a cloud solution** | ~30% | Day-2 operations: manage compute/storage/network, monitoring & logging |
| **4. Configuring access and security** | ~20% | IAM policies, roles, service accounts, Workload/Workforce Identity Federation |

> 💡 **Exam Tip:** Domains 2 + 3 = 60% of the exam. If you only have time to master two areas deeply, make it compute/storage/network design (Domain 2) and monitoring/logging + day-2 ops (Domain 3). Domain 1 and 4 are narrower but very detail-heavy (IAM nuances) — easy marks if you drill them.

---

## 0.3 What Changed — Post–June 30, 2026 Update vs. the Older Guide

Google refreshed the ACE guide effective for exams taken on or after June 30, 2026, mainly to reflect rebranding and the new AI-assisted tooling layer. If you studied from an older (2024/2025) course, note these deltas:

* **Vertex AI → Gemini Enterprise Agent Platform:** Google announced this rebrand on April 22, 2026. Old name *'Vertex AI Agent Engine'* is now **Agent Runtime on Gemini Enterprise Agent Platform**; *'Vertex AI Workbench'* is now **Gemini Enterprise Agent Platform Workbench**. The exam guide uses the new names with the old name in parentheses — expect either to appear.
* **New AI-Assisted Tooling Objective (2.4):** Explicitly names **Gemini CLI**, **Google Antigravity**, **Gemini Cloud Assist**, and **Application Design Center** — these are agentic/AI dev & ops tools, a first for the ACE.
* **Cloud NGFW (Cloud Next Generation Firewall):** Now named explicitly alongside classic VPC firewall rules. Tiers (Essentials/Standard/Enterprise), Tags, and service-account-based targeting are highly testable.
* **New/Renamed Data & Storage Products:** Added **Google Cloud Managed Lustre**, **Google Cloud NetApp Volumes**, **Google Cloud Managed Service for Apache Kafka**, and **AlloyDB** (now a core focus product).
* **New Operations Tooling:** **Database Center** (fleet-wide DB health), **Cloud Hub** (unified ops/health/cost/security dashboard), **Personalized Service Health**, **Active Assist**, and **Query Insights + Index Advisor**.
* **Workforce Identity Federation:** External human identities (e.g., corporate SSO → GCP) is now called out separately from **Workload Identity Federation** (external/GKE workloads → GCP). This is a classic confusable pair (see Part 9 for a full breakdown).
* **App Engine Legacy Status:** App Engine has quietly receded from the guide's explicit compute list (now: Compute Engine, GKE, Cloud Run, Cloud Run functions, Agent Runtime). It is no longer a primary teaching objective.
* **Cloud Run Functions:** Cloud Functions has been renamed to **Cloud Run functions** to reflect that functions now run on the Cloud Run stack (gen2 execution model).
* **Hyperdisk:** Joins zonal/regional Persistent Disk as a first-class Compute Engine storage option.

---

## 0.4 How This Handbook Is Organized

* **Logical Build Path:** Parts 1–13 map directly to how you should build knowledge: fundamentals → the 4 official domains → cross-cutting reference → revision → practice → career.
* **AWS ↔ GCP Easing:** Every AWS-relevant concept carries a blue **AWS ↔ GCP TIP** box — skip these once a concept feels native to you.
* **High-Yield Callouts:** Amber **EXAM TIP** boxes flag what's actually asked on the exam, while red **COMMON MISTAKE** boxes flag traps test-writers love.
* **Under the Hood Insights:** Green **UNDER THE HOOD** boxes explain the *why*, which makes scenario questions solvable even when the exact wording is unfamiliar.
* **Cheat Sheets & Practices:** Part 6 (Rosetta Stone) is your single-page service map, Part 7 is your command reference guide, and Part 12 contains exam-style scenario questions with detailed rationales.

---

> [🏠 Back to README](../README.md) | [Part 1: GCP Fundamentals & Mental Model (For an AWS SAA Holder) ▶](./part-01-gcp-fundamentals.md)
