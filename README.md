# Falcon Cloud Security — Starter Workflows

Four ready-to-import Fusion SOAR workflows for customers getting started with Falcon Cloud Security and WAF monitoring.

---

## Workflows

| File | Trigger | Purpose |
|------|---------|---------|
| `01-cloud-risk-critical-alert.yaml` | Cloud risk response event | Email on Critical/High compound cloud risks |
| `02-iom-detection-triage.yaml` | IOM detection event | Email on new cloud misconfigurations (Medium+) |
| `03-waf-iom-detection-alert.yaml` | IOM detection event | Email on WAF misconfiguration IOMs (AWS WAFv2, Azure App GW WAF, etc.) |
| `04-cloud-risk-daily-digest.yaml` | Schedule (8 AM UTC daily) | Daily cloud security review checklist |

---

## How to Import

1. In the Falcon console, go to **Fusion SOAR > Fusion SOAR > Workflows**
2. Click **Create workflow**
3. Select **Import workflow** and upload the YAML file
4. Follow the in-file `SETUP REQUIRED` steps (primarily: set your email address)
5. Use **Run on demand** or the mock data tester to validate before enabling

---

## Before You Enable Each Workflow

Each file contains a `SETUP REQUIRED` section in its `description:` block. At minimum:

- Replace `@your-company.com` email placeholders with real addresses
  (invalid addresses cause runtime failures — Fusion SOAR does not warn at import time)
- For `03-waf-threat-detection-alert`: configure your WAF data connector in Next-Gen SIEM first,
  then inspect a live trigger payload to confirm the exact field names for the source filter condition

---

## Notes on WAF Coverage

Workflow `03` is a **Cloud Security posture (IOM) workflow** — it triggers on WAF
*misconfigurations* detected by Falcon CSPM, not on WAF attack traffic.

It catches things like:
- AWS WAFv2 web ACL not associated with an ALB, CloudFront, or API Gateway
- AWS WAF logging disabled
- Azure Application Gateway WAF in Detection mode instead of Prevention
- Azure Front Door with no WAF policy attached
- GCP backend services without a Cloud Armor policy

**Finding your WAF policy IDs:** The exact service names and policy IDs vary by cloud
provider and Falcon tenant. Before enabling, navigate to:
`Cloud Security > Rules and Policies > IOM Rules` and filter by Service = WAF / WAFv2 /
Application Gateway to see which rules apply to your registered accounts. Update the
`waf_service_filter` condition in the workflow to match.

---

## Extending These Workflows

These are intentionally minimal starting points. Common next steps:

- Add a **ServiceNow** or **Jira** action after the email step to auto-create tickets
- Add a **Slack** notification action (via inbound webhook) for real-time channel alerts
- Chain `01` and `02` into a triage workflow that enriches findings with asset inventory data
- For AWS environments: add **AWS SOAR Actions** (EC2, IAM, S3) to automate remediation —
  see Falcon Console > CrowdStrike Store > search "AWS SOAR Actions"

---

## Prerequisites Summary

| Requirement | Workflows |
|-------------|-----------|
| Falcon Cloud Security subscription | 01, 02, 03 |
| At least one AWS account registered in Falcon Cloud Security | 01, 02 |
| AWS or Azure/GCP account registered for WAF IOM coverage | 03 |
| Any Falcon Cloud Security subscription | 04 |
| Workflow Author role in Falcon | All |
