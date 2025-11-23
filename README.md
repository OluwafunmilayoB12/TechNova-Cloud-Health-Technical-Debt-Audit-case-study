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

📎 **Visual Reference:** ![Decision Table](https://github.com/OluwafunmilayoB12/Project/blob/main/DecisionTable.png)

---

## DATA CLEANING – 4-PHASE PROCESS

**Phase 1 – Date Standardization**  
Mixed text formats in `line_item_usage_start_date` were unified using `TRY_CAST(line_item_usage_start_date AS Date)`.

**Phase 2 – Numeric Corrections**  
`line_item_blended_cost` contained text and negatives. We cast to FLOAT and applied `ABS()`.

**Phase 3 – Text Standardization**  
Team names and environments were upper-cased and trimmed to ensure consistent joins

**Phase 4 – Logical Integrity**  
Removed null CPU values and flagged resources active after decommission dates (“zombie” servers)

---

## HYPOTHESIS EXPLORATION

Before analysis, the following are tested hypotheses:

1. **Servers are overloaded → False** – no overloaded servers in the environment
2.  **Most legacy servers are underutilized → True** – Indicating technical debt
3. **Majority of teams are still running on legacy server → True** – The five teams contributed to the modernization debt 

Applied the **BAIIR** Framework (*Baseline, Analysis, Insight, Impact, Recommendation*) document the analysis for clarity.

---

## ANALYSIS

### Queries Used
```sql
-- Baseline trend

Resources vulnerability rate
  No of resources with at least 1 vulnerabilty / Actual server  *100

  WITH total_resource AS(
		SELECT COUNT(DISTINCT resource_id) AS Actual_server
		FROM resource_configuration
		WHERE Modernization_status IN ('Legacy', 'Modern')
),
    vulnerable_resources AS(
		SELECT COUNT(DISTINCT resource_id) AS vulnerable_resources
		FROM security_findings_cleaned
)
	SELECT vr.vulnerable_resources, tr.Actual_server,
			(vr.vulnerable_resources * 100.0 / tr.Actual_server) as vulnerability_rate
	FROM total_resource tr
	CROSS JOIN vulnerable_resources vr;


