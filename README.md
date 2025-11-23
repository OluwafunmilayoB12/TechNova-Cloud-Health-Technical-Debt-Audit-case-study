#  TechNova Cloud Health & Technical Debt Audit Case Study
*A Data-Driven Investigation into Cloud resources security vulnerabilities, server utilization and modernization debt metrics*

---

## CONTEXT AND PROBLEM

TechNova Software Inc. is a fast-growing SaaS company based in Nairobi, Kenya.  
Recently, leadership noticed that **the technical debt of 33% was accumulated during the period of growth at all cost** between July and August 2025, engineering leadership was facing significant **operational friction**.

### Problem Statement
 TechNova is experiencing significant operational friction and accumulated technical debt that built up during the "growth-at-all-cost" phase, this affects the CTO & Engineering Managers by increasing the platform inefficiency and stability.
 We need to run Root Cause Analysis & trend analysis to perform a full audit of all cloud resources,
 to identify and reduce technical debt and critical vulnerabilities, aiming to lower the percentage of affected resources to below 5% and decrease the total vulnerability count by at least 60% after remediation.

### Stakeholders

| Role | Responsibility |
|------|----------------|
| **CTO** | Oversees platform reliability and technical efficiency |
| **Engineering Managers** | coordinate teams to prioritize fixes  |
| **Data Analyst (You)** | Diagnose causes, quantify risks, communicate insights |

---

## BUSINESS IMPACT

| Metric | Value | Interpretation |
|---------|--------|----------------|
| Resource Vulnerability Rate  | 25% | exposure to security risk |
| Critical Vulnerability Count | 704| High number of severe vulnerabilities that require urgent fix  |
| Total vulnerability | 3,416 | security vulnerabilities across the cloud environment needed to be fixed|
| Healthy server | 11,464 | support stable platfrom operations |
| Idle server | 2,166 | Large number of underutilized server generating unnecessary cost, to be shutdown |
| Actual server | 13,698 | active servers  |
| Modernization Debt | 33% | servers are still running on outdated technology, increasing technical debt |
| Legacy server | 4,556 | more prone to inefficiency and vulnerabilities |
| Modern server | 9,142 | updated server supporting better performance |

---

Dataset Metadata
The project uses six CSV files, but focuses on specific columns to answer the technical questions.

| Table | Description | Key Columns |
|--------|--------------|-------------|
| **cost_and_usage_report** | The detailed daily financial log | `line_item_resource_id`, `line_item_blended_cost`, `usage_date` |
| **resource_tags** | Maps resources to team / project | `resource_tag_team`, `resource_tag_project` |
| **performance_metrics** | CPU utilization data | `resource_id`, `average_value`, `metric_date` |
| **security_findings_cleaned** | Lists security vulnerabilities | `resource_id`, `severity` |
| **resource_configuration** | Instance types & decommission dates | `resource_id`, `instance_type`, `decommission_date` |

---

## 🧾 DECISION TABLE — DATA QUALITY & CLEANING STRATEGY

📎 **Visual Reference:**
