# Part 2 — DOMAIN 1: Setting Up a Cloud Solution Environment (~20%)

Covers: 1.1 Setting up cloud projects & accounts, 1.2 Managing billing configuration.

## 2.1 Creating a Resource Hierarchy

- Design pattern: Org → Folders per environment or business unit → Projects per team/app/environment. Common folder patterns: by environment (prod/staging/dev), by team, or by business unit — pick one and stay consistent (mixing patterns is a real-world and exam anti-pattern).
- Projects are cheap and fast to create — prefer more, smaller projects for blast-radius isolation (billing, IAM, quota) over one giant shared project.
- Creating an Org requires a Cloud Identity or Google Workspace account tied to a verified domain.
```bash
gcloud projects create PROJECT_ID --folder=FOLDER_ID --organization=ORG_ID
```

> 💡 **Exam Tip:** Know the command-line trio: gcloud organizations list, gcloud resource-manager folders create --display-name=X --organization=Y, gcloud projects create. The exam likes asking “you need to create X under Y with the fewest steps” — these direct commands beat multi-step console click-paths.

## 2.2 Applying Organization Policies to the Hierarchy

- Set via gcloud resource-manager org-policies set-policy or the Console's Organization Policies page, targeted at Org/Folder/Project level.
- Common exam-relevant constraints: constraints/compute.vmExternalIpAccess (restrict public IPs), constraints/compute.restrictVpcPeering, constraints/iam.disableServiceAccountKeyCreation, constraints/gcp.resourceLocations (restrict allowed regions).
- A constraint can be inherited, overridden (if allowed), or explicitly enforced/‘not enforced’ at a node.

## 2.3 Granting IAM Roles Within a Project

- At setup time you'll typically grant: Owner/Editor to initial admins (sparingly!), then move to predefined roles (e.g., roles/compute.instanceAdmin.v1, roles/storage.objectViewer) as you productionize.
```bash
gcloud projects add-iam-policy-binding PROJECT_ID --member='user:x@y.com' --role='roles/viewer'
```

- Full IAM depth is in Part 5 — at setup time, just know: grant least privilege from day one, prefer Groups over individual user bindings for maintainability.

## 2.4 Managing Users & Groups in Cloud Identity

- Cloud Identity = Google's free IdP-like service providing user/group management without needing full Google Workspace (no Gmail/Docs, just identity).
- Manual: create users/groups in the Admin Console (admin.google.com).
- Automated: sync from an existing on-prem/Azure AD (Entra ID) via Google Cloud Directory Sync (GCDS), or via SCIM-based provisioning, or via Workforce Identity Federation (federate an external IdP directly, no user copy needed — see 2.11).
- Always assign IAM roles to Google Groups, not individual users — easier onboarding/offboarding, matches least-privilege-at-scale best practice tested repeatedly on the exam.
> 📘 **AWS ↔ GCP Tip:** Cloud Identity ↔ roughly AWS IAM Identity Center (SSO) + a directory, though Cloud Identity is a distinct product from Google Workspace-lite. Groups-over-users is the same best practice AWS teaches (IAM Groups / SSO Permission Sets).

## 2.5 Enabling APIs Within Projects

- Most GCP services must be explicitly enabled per-project before use — a very common “why isn't this working” exam scenario (“access denied” that's actually “API not enabled”).
```bash
gcloud services enable compute.googleapis.com
```

```bash
gcloud services list --available / --enabled
```

- Enabling an API does not by itself grant permissions — you still need IAM roles. Both conditions must be true: API enabled AND caller has the IAM permission.
> ⚠️ **Common Mistake:** Classic trap question: “User has Editor role but gets a 403/permission error calling a new API.” First suspect: the API isn't enabled on the project, not an IAM problem. Always check both.

---

> [◀ Part 1: GCP Fundamentals & Mental Model](./part-01-gcp-fundamentals.md) | [🏠 Back to README](../README.md) | [Part 2b: Operations, Networking & Billing ▶](./part-02b-operations-networking-billing.md)
