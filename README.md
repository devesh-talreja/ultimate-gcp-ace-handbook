# ☁️ Google Cloud Associate Cloud Engineer (ACE) | Ultimate Study Handbook/Cheatsheet (Updated: July 2026)

[![GCP](https://img.shields.io/badge/Google%20Cloud-ACE-blue?style=flat&logo=googlecloud)](https://cloud.google.com/learn/certification/cloud-engineer)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Modules](https://img.shields.io/badge/Modules-22-blue)]()
[![Platform](https://img.shields.io/badge/Exam%20Update-Post--June%2030%2C%202026-orange)]()

---

## 📖 About This Handbook

This study handbook and comprehensive revision notes were compiled during my preparation for the **Google Cloud Associate Cloud Engineer (ACE)** certification, updated specifically for the **post-June 30, 2026 exam refresh**. These notes serve as a high-yield study companion to reinforce core concepts, memorize key command-line syntax, and highlight tricky exam traps. I've used them heavily during revision — going through each section multiple times to reinforce concepts, memorize key differences, and drill exam traps, with AI helping me articulate and consolidate concepts as I studied.

**What makes these notes highly effective:**
* **Exam-Focused Callouts:** Styled with native GitHub blockquote alerts for clear visual hierarchy: green **Exam Tips** `[!TIP]` 💡, blue **AWS ↔ GCP Tips** `[!NOTE]` 📘, red **Common Mistakes** `[!CAUTION]` ⚠️, and purple **Under the Hood** `[!IMPORTANT]` ⚙️ insights.
* **40+ Exam-Style Practice Questions:** Features forty original practice scenarios with full collapsible explanations to test your decision logic before the real exam.
* **Smart Modularization:** Dense architectural topics (Compute, GKE, Storage, Operations, and Practice Questions) are divided into bite-sized, readable modules rather than single, massive pages.
* **Fully Formatted Data:** Utilizes clean comparison tables, command lists, and step-by-step logic workflows.
* **Rosetta Stone Reference:** Contains a full-service translation map between AWS and GCP for rapid mental model mapping.

---

> 💡 **AWS Transition Note:** While this guide serves as a comprehensive study path for all GCP candidates, it is also highly valuable for AWS veterans. It uses side-by-side service mappings (like the [Rosetta Stone in Part 6](modules/part-06-rosetta-stone.md)) to help map your AWS knowledge to GCP's global-VPC and hierarchy models.

---

## 📚 Table of Contents

| # | Part / Section | Key Topics & Sub-topics |
|---|----------------|-------------------------|
| 0 | **[Part 0: Exam Blueprint & How To Use This Handbook](modules/part-00-exam-blueprint.md)** | • [0.1 Exam Snapshot (Standard Exam, valid on/after June 30, 2026)](modules/part-00-exam-blueprint.md#01-exam-snapshot-standard-exam-valid-onafter-june-30-2026)<br>• [0.2 Domain Weightings (official, post–June 30 2026 guide)](modules/part-00-exam-blueprint.md#02-domain-weightings-official-post-june-30-2026-guide)<br>• [0.3 What Changed — Post–June 30, 2026 Update vs. the Older Guide](modules/part-00-exam-blueprint.md#03-what-changed-post-june-30-2026-update-vs-the-older-guide)<br>• [0.4 How This Handbook Is Organized](modules/part-00-exam-blueprint.md#04-how-this-handbook-is-organized) |
| 1 | **[Part 1: GCP Fundamentals & Mental Model (For an AWS SAA Holder)](modules/part-01-gcp-fundamentals.md)** | • [1.1 The Core Mindset Shift](modules/part-01-gcp-fundamentals.md#11-the-core-mindset-shift)<br>• [1.2 GCP Global Infrastructure](modules/part-01-gcp-fundamentals.md#12-gcp-global-infrastructure)<br>• [1.3 Resource Hierarchy](modules/part-01-gcp-fundamentals.md#13-resource-hierarchy)<br>• [1.4 Organization Policies (Org Policy Service)](modules/part-01-gcp-fundamentals.md#14-organization-policies-org-policy-service)<br>• [1.5 Ways to Interact with GCP](modules/part-01-gcp-fundamentals.md#15-ways-to-interact-with-gcp)<br>• [1.6 gcloud CLI Structure You Must Know Cold](modules/part-01-gcp-fundamentals.md#16-gcloud-cli-structure-you-must-know-cold)<br>• [1.7 Billing Fundamentals (Preview — full detail in Part 2)](modules/part-01-gcp-fundamentals.md#17-billing-fundamentals-preview-full-detail-in-part-2)<br>• [1.8 Mini AWS ↔ GCP Terminology Map (see Part 6 for the full Rosetta Stone)](modules/part-01-gcp-fundamentals.md#18-mini-aws-gcp-terminology-map-see-part-6-for-the-full-rosetta-stone) |
| 2 | **[Part 2: DOMAIN 1: Setting Up a Cloud Solution Environment (~20%)](modules/part-02a-resource-hierarchy-and-access.md)** | • [2.1 Creating a Resource Hierarchy](modules/part-02a-resource-hierarchy-and-access.md#21-creating-a-resource-hierarchy)<br>• [2.2 Applying Organization Policies to the Hierarchy](modules/part-02a-resource-hierarchy-and-access.md#22-applying-organization-policies-to-the-hierarchy)<br>• [2.3 Granting IAM Roles Within a Project](modules/part-02a-resource-hierarchy-and-access.md#23-granting-iam-roles-within-a-project)<br>• [2.4 Managing Users & Groups in Cloud Identity](modules/part-02a-resource-hierarchy-and-access.md#24-managing-users-groups-in-cloud-identity)<br>• [2.5 Enabling APIs Within Projects](modules/part-02a-resource-hierarchy-and-access.md#25-enabling-apis-within-projects) |
| 3 | **[Part 2b: Operations, Networking & Billing](modules/part-02b-operations-networking-billing.md)** | • [2.6 Provisioning Google Cloud Observability](modules/part-02b-operations-networking-billing.md#26-provisioning-google-cloud-observability)<br>• [2.7 Assessing Quotas & Requesting Increases](modules/part-02b-operations-networking-billing.md#27-assessing-quotas-requesting-increases)<br>• [2.8 Setting Up Standalone Organizations](modules/part-02b-operations-networking-billing.md#28-setting-up-standalone-organizations)<br>• [2.9 Setting Up Cloud Networking (Initial)](modules/part-02b-operations-networking-billing.md#29-setting-up-cloud-networking-initial)<br>• [2.10 Verifying Product Availability Across Locations](modules/part-02b-operations-networking-billing.md#210-verifying-product-availability-across-locations)<br>• [2.11 Cloud Asset Inventory + Gemini Cloud Assist](modules/part-02b-operations-networking-billing.md#211-cloud-asset-inventory-gemini-cloud-assist)<br>• [2.12 Workforce Identity Federation](modules/part-02b-operations-networking-billing.md#212-workforce-identity-federation)<br>• [2.13 Billing Configuration (Objective 1.2)](modules/part-02b-operations-networking-billing.md#213-billing-configuration-objective-12)<br>• [2.14 Domain 1 Quick-Fire Recap](modules/part-02b-operations-networking-billing.md#214-domain-1-quick-fire-recap) |
| 4 | **[Part 3: DOMAIN 2: Planning & Implementing a Cloud Solution (~30%)](modules/part-03a-compute-engine.md)** | • [3.1 The Compute Decision Framework (memorize this first)](modules/part-03a-compute-engine.md#31-the-compute-decision-framework-memorize-this-first)<br>• [3.2 Compute Engine — Deep Dive](modules/part-03a-compute-engine.md#32-compute-engine-deep-dive) |
| 5 | **[Part 3b: GKE & Serverless Compute](modules/part-03b-gke-and-serverless.md)** | • [3.3 GKE (Google Kubernetes Engine) — Deep Dive](modules/part-03b-gke-and-serverless.md#33-gke-google-kubernetes-engine-deep-dive)<br>• [3.4 Serverless Compute — Cloud Run & Cloud Run Functions](modules/part-03b-gke-and-serverless.md#34-serverless-compute-cloud-run-cloud-run-functions) |
| 6 | **[Part 3c: Storage & Databases](modules/part-03c-storage-and-databases.md)** | • [3.5 Storage & Data Products — The Master Decision Matrix](modules/part-03c-storage-and-databases.md#35-storage-data-products-the-master-decision-matrix)<br>• [3.6 Cloud Storage Options](modules/part-03c-storage-and-databases.md#36-cloud-storage-options)<br>• [3.7 Filestore, NetApp Volumes, Managed Lustre — File Storage Trio](modules/part-03c-storage-and-databases.md#37-filestore-netapp-volumes-managed-lustre-file-storage-trio)<br>• [3.8 Loading Data](modules/part-03c-storage-and-databases.md#38-loading-data)<br>• [3.9 Multi-Region Redundancy Across Data Solutions](modules/part-03c-storage-and-databases.md#39-multi-region-redundancy-across-data-solutions) |
| 7 | **[Part 3d: VPC Networking & Planning Tools](modules/part-03d-networking-and-tooling.md)** | • [3.10 Networking — Deep Dive](modules/part-03d-networking-and-tooling.md#310-networking-deep-dive)<br>• [3.11 Planning & Implementing with Tooling (Objective 2.4)](modules/part-03d-networking-and-tooling.md#311-planning-implementing-with-tooling-objective-24)<br>• [3.12 Domain 2 Quick-Fire Recap](modules/part-03d-networking-and-tooling.md#312-domain-2-quick-fire-recap) |
| 8 | **[Part 4: DOMAIN 3: Ensuring Successful Operation of a Cloud Solution (~30%)](modules/part-04a-resource-management.md)** | • [4.1 Managing Compute Resources](modules/part-04a-resource-management.md#41-managing-compute-resources)<br>• [4.2 Managing Storage & Data Solutions](modules/part-04a-resource-management.md#42-managing-storage-data-solutions)<br>• [4.3 Managing Networking Resources](modules/part-04a-resource-management.md#43-managing-networking-resources) |
| 9 | **[Part 4b: Cloud Monitoring & Logging](modules/part-04b-monitoring-and-logging.md)** | • [4.4 Monitoring & Logging — The Biggest Sub-Objective (read this section twice)](modules/part-04b-monitoring-and-logging.md#44-monitoring-logging-the-biggest-sub-objective-read-this-section-twice)<br>• [4.5 Domain 3 Quick-Fire Recap](modules/part-04b-monitoring-and-logging.md#45-domain-3-quick-fire-recap) |
| 10 | **[Part 5: DOMAIN 4: Configuring Access and Security (~20%)](modules/part-05-access-and-security.md)** | • [5.1 IAM Core Concepts](modules/part-05-access-and-security.md#51-iam-core-concepts)<br>• [5.2 Managing Service Accounts (heavily tested)](modules/part-05-access-and-security.md#52-managing-service-accounts-heavily-tested)<br>• [5.3 Domain 4 Quick-Fire Recap](modules/part-05-access-and-security.md#53-domain-4-quick-fire-recap) |
| 11 | **[Part 6: The AWS ↔ GCP Rosetta Stone (Master Reference Table)](modules/part-06-rosetta-stone.md)** | • [6.1 Compute](modules/part-06-rosetta-stone.md#61-compute)<br>• [6.2 Storage](modules/part-06-rosetta-stone.md#62-storage)<br>• [6.3 Databases & Data](modules/part-06-rosetta-stone.md#63-databases-data)<br>• [6.4 Networking](modules/part-06-rosetta-stone.md#64-networking)<br>• [6.5 IAM & Security](modules/part-06-rosetta-stone.md#65-iam-security)<br>• [6.6 Monitoring, DevOps & Management](modules/part-06-rosetta-stone.md#66-monitoring-devops-management) |
| 12 | **[Part 7: gcloud & kubectl Command Reference (High-Frequency Commands)](modules/part-07-command-reference.md)** | • [7.1 Setup & Config](modules/part-07-command-reference.md#71-setup-config)<br>• [7.2 Compute Engine](modules/part-07-command-reference.md#72-compute-engine)<br>• [7.3 GKE / kubectl](modules/part-07-command-reference.md#73-gke-kubectl)<br>• [7.4 Storage, Data, Networking, IAM](modules/part-07-command-reference.md#74-storage-data-networking-iam) |
| 13 | **[Part 8: Architecture Decision Scenarios (Exam-Pattern Walkthroughs)](modules/part-08-decision-scenarios.md)** | • [8.1 Compute & Scaling Scenarios](modules/part-08-decision-scenarios.md#81-compute-scaling-scenarios)<br>• [8.2 Storage & Data Scenarios](modules/part-08-decision-scenarios.md#82-storage-data-scenarios)<br>• [8.3 Networking Scenarios](modules/part-08-decision-scenarios.md#83-networking-scenarios)<br>• [8.4 IAM & Security Scenarios](modules/part-08-decision-scenarios.md#84-iam-security-scenarios)<br>• [8.5 Monitoring & Operations Scenarios](modules/part-08-decision-scenarios.md#85-monitoring-operations-scenarios) |
| 14 | **[Part 9: Confusing & Similar Concepts, Clarified Side-by-Side](modules/part-09-confusing-concepts.md)** | • [9.1 Workforce Identity Federation vs. Workload Identity Federation](modules/part-09-confusing-concepts.md#91-workforce-identity-federation-vs-workload-identity-federation)<br>• [9.2 Zonal vs. Regional Persistent Disk vs. Hyperdisk vs. Local SSD](modules/part-09-confusing-concepts.md#92-zonal-vs-regional-persistent-disk-vs-hyperdisk-vs-local-ssd)<br>• [9.3 Custom Mode vs. Auto Mode VPC](modules/part-09-confusing-concepts.md#93-custom-mode-vs-auto-mode-vpc)<br>• [9.4 Predefined vs. Custom vs. Basic IAM Roles](modules/part-09-confusing-concepts.md#94-predefined-vs-custom-vs-basic-iam-roles)<br>• [9.5 GKE Autopilot vs. GKE Standard](modules/part-09-confusing-concepts.md#95-gke-autopilot-vs-gke-standard)<br>• [9.6 Snapshot vs. Image](modules/part-09-confusing-concepts.md#96-snapshot-vs-image)<br>• [9.7 Live Migration vs. Automatic Restart](modules/part-09-confusing-concepts.md#97-live-migration-vs-automatic-restart)<br>• [9.8 Hierarchical Firewall Policy vs. Network Firewall Policy vs. VPC Firewall Rule](modules/part-09-confusing-concepts.md#98-hierarchical-firewall-policy-vs-network-firewall-policy-vs-vpc-firewall-rule)<br>• [9.9 HPA vs. VPA vs. Cluster Autoscaler vs. Node Auto-Provisioning](modules/part-09-confusing-concepts.md#99-hpa-vs-vpa-vs-cluster-autoscaler-vs-node-auto-provisioning)<br>• [9.10 CMEK vs. CSEK vs. Google-Managed Encryption](modules/part-09-confusing-concepts.md#910-cmek-vs-csek-vs-google-managed-encryption)<br>• [9.11 Cloud Run vs. Cloud Run Functions vs. App Engine (legacy)](modules/part-09-confusing-concepts.md#911-cloud-run-vs-cloud-run-functions-vs-app-engine-legacy)<br>• [9.12 Database Center vs. Cloud Hub vs. Personalized Service Health vs. Active Assist](modules/part-09-confusing-concepts.md#912-database-center-vs-cloud-hub-vs-personalized-service-health-vs-active-assist) |
| 15 | **[Part 10: High-Yield Last-Minute Revision Notes](modules/part-10-revision-notes.md)** | • [10.1 Numbers to Memorize](modules/part-10-revision-notes.md#101-numbers-to-memorize)<br>• [10.2 One-Line Service Identity Card](modules/part-10-revision-notes.md#102-one-line-service-identity-card)<br>• [10.3 The 10 Decisions That Show Up Most Often](modules/part-10-revision-notes.md#103-the-10-decisions-that-show-up-most-often) |
| 16 | **[Part 11: Common Mistakes & Exam Traps](modules/part-11-exam-traps.md)** | • [11.1 The “Sounds Right But Isn't” Traps](modules/part-11-exam-traps.md#111-the-sounds-right-but-isnt-traps)<br>• [11.2 Keyword → Answer Reflexes](modules/part-11-exam-traps.md#112-keyword-answer-reflexes) |
| 17 | **[Part 12: Practice Questions by Domain](modules/part-12a-practice-questions-domains-1-2.md)** | • [12.1 Domain 1 — Setting Up a Cloud Solution Environment](modules/part-12a-practice-questions-domains-1-2.md#121-domain-1-setting-up-a-cloud-solution-environment)<br>• [12.2 Domain 2 — Planning & Implementing a Cloud Solution](modules/part-12a-practice-questions-domains-1-2.md#122-domain-2-planning-implementing-a-cloud-solution)<br>• [Domain 1 & 2 Speed Drill](modules/part-12a-practice-questions-domains-1-2.md#domain-1-2-speed-drill) |
| 18 | **[Part 12b: Practice Questions (Domains 3 & 4)](modules/part-12b-practice-questions-domains-3-4.md)** | • [12.3 Domain 3 — Ensuring Successful Operation of a Cloud Solution](modules/part-12b-practice-questions-domains-3-4.md#123-domain-3-ensuring-successful-operation-of-a-cloud-solution)<br>• [12.4 Domain 4 — Configuring Access and Security](modules/part-12b-practice-questions-domains-3-4.md#124-domain-4-configuring-access-and-security)<br>• [12.5 AWS-Lens Trap Questions (Specifically for AWS-Background Candidates)](modules/part-12b-practice-questions-domains-3-4.md#125-aws-lens-trap-questions-specifically-for-aws-background-candidates) |
| 19 | **[Part 13: AWS → GCP Transition: Practical Study Plan](modules/part-13-study-plan.md)** | • [13.1 Realistic Timeline](modules/part-13-study-plan.md#131-realistic-timeline)<br>• [13.2 Hands-On Labs That Matter Most](modules/part-13-study-plan.md#132-hands-on-labs-that-matter-most)<br>• [13.3 Free-Tier & Cost-Control Tips for a Student Budget](modules/part-13-study-plan.md#133-free-tier-cost-control-tips-for-a-student-budget)<br>• [13.4 Mindset Traps Specific to AWS Veterans](modules/part-13-study-plan.md#134-mindset-traps-specific-to-aws-veterans) |
| 20 | **[Part 14: Beyond Certification: Real-World Growth in Agentic AI, Cloud & DevOps (2026 Trends)](modules/part-14-growth-trends.md)** | • [14.1 The Trend: Cloud Engineering Is Merging With Agentic AI](modules/part-14-growth-trends.md#141-the-trend-cloud-engineering-is-merging-with-agentic-ai)<br>• [14.2 Project Ideas That Combine ACE Skills + Agentic AI (Portfolio-Worthy)](modules/part-14-growth-trends.md#142-project-ideas-that-combine-ace-skills-agentic-ai-portfolio-worthy)<br>• [14.3 Career Path: ACE → Where Next](modules/part-14-growth-trends.md#143-career-path-ace-where-next)<br>• [14.4 Staying Current After the Exam](modules/part-14-growth-trends.md#144-staying-current-after-the-exam) |
| 21 | **[Part 15: Final Checklist Before Exam Day](modules/part-15-exam-checklist.md)** | • [15.1 One Week Before](modules/part-15-exam-checklist.md#151-one-week-before)<br>• [15.2 Day Before](modules/part-15-exam-checklist.md#152-day-before)<br>• [15.3 During the Exam](modules/part-15-exam-checklist.md#153-during-the-exam) |

---

## 🚀 How to Use This Guide

1. **Understand the Blueprint:** Start with [Part 0: Exam Blueprint & How To Use This Handbook](modules/part-00-exam-blueprint.md) to understand the domain weightings and recent updates.
2. **Build the Fundamentals:** If you have an AWS background, read [Part 1: GCP Fundamentals & Mental Model](modules/part-01-gcp-fundamentals.md) to shift your mind from AWS's regional-VPC thinking to GCP's global-VPC architecture and project-based hierarchy.
3. **Study by Domain:** Work through the core exam domains (Parts 2 to 5) at your own pace.
4. **Compare Side-by-Side:** Utilize [Part 9: Side-by-Side Concept Clarifications](modules/part-09-confusing-concepts.md) to clear up confusing GKE autoscaling details or Identity Federation types.
5. **Drill the Commands:** Review the [Part 7: Command Reference Guide](modules/part-07-command-reference.md) and practice typing the syntax.
6. **Test Yourself:** Drill the extensive practice scenarios in [Part 12a](modules/part-12a-practice-questions-domains-1-2.md) and [Part 12b](modules/part-12b-practice-questions-domains-3-4.md).
7. **Practice Labs & Mock Exams (Crucial):** While this guide provides the theoretical blueprint, hands-on experience and simulated practice tests are highly recommended to ensure a pass:
    * Complete the official [Google Cloud Skills Boost: GCP Associate Cloud Engineer Learning Path](https://www.cloudskillsboost.google/paths/11) to gain invaluable hands-on lab experience.
    * It is also highly recommended to drill mock practice exams to build speed and test your knowledge against realistic scenario-based questions before the actual test.

---
### Navigation

Every section file has **← prev | README | next →** links at the bottom so you can move through sections without coming back to this page.

⭐ **Star this Repo:** If this handbook helps you in your GCP journey, please give it a star and bookmark it so you can easily return to it during your revision rounds!

---

## 🔗 Official GCP Resources & Reference Links

To supplement your learning, bookmark these popular and official resources:
* **[Official GCP ACE Certification Portal](https://cloud.google.com/learn/certification/cloud-engineer/)** - The official homepage and registration page for the exam.
* **[Google Cloud Skills Boost](https://www.cloudskillsboost.google/)** - Official portal for Qwiklabs, hands-on environments, and learning paths.
* **[Cloud Girl (Priyanka Vergadia) Visual Diagrams](https://thecloudgirl.dev/)** - Excellent graphical mindmaps and service breakdowns.
* **[Google Cloud Documentation](https://cloud.google.com/docs)** - The ultimate source of truth for technical definitions.

---

## 🤝 Contributing

Contributions are welcome! If you spot a typo, an outdated CLI flag, or have a better explanation/mnemonic to add, please:
1. Fork this repository.
2. Create a feature branch.
3. Submit a Pull Request.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Happy Learning and best of luck conquering the GCP ACE exam! 🚀 Gambare!** *- Devesh Talreja*
