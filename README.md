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
| Resource Vulnerability Rate  | 25% | *Increased from a baseline of 15% to 25%*, showing **10% increase** in exposed resources to security risk |
| Critical Vulnerability Count | 704| High number of severe vulnerabilities that require urgent fix  |
| Total vulnerability | 3,416 | security vulnerabilities across the cloud environment needed to be fixed|
| Healthy server | 11,464 | Healthy servers *ranged from 2-39 between June 30 and Aug 27,spiked to 494 on Aug 28*, then stabilized at 10,343 on Aug 29|
| Idle server | 2,166 | *idle servers jumped from 0-6 (Jun 30- Aug 27)* to **2,061 in two days (Aug 28-29)** generating unnecessary cost and resource inefficiency |
| Actual server | 13,698 | Out of 13,698 servers, 9,142 are modern and 4,556 are older. Most of the server is fine but the older servers lead to business risks  |
| Modernization Debt | 33% | *One-third of the servers* are still running on outdated technology, increasing technical debt across all teams |
| Legacy server | 4,556 | 33% of servers that are outdated causing inefficiency |
| Modern server | 9,142 | 67% of updated server supporting better performance |

---

## Dataset Metadata
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

Applied the **BAIIR** Framework (*Baseline, Analysis, Insight, Impact, Recommendation*) to document the analysis for clarity.

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
```

---

## RESULTS AND VISUALS

### VISUAL 1 - Cloud Vulnerability Overview
![Visual 1](https://github.com/OluwafunmilayoB12/Project/blob/main/Visual%201.png)

### VISUAL 2 Performance
![Visual 2](https://github.com/OluwafunmilayoB12/Project/blob/main/Visual%202.png)

### VISUAL 3 - Io Fix and Recommendation
![Visual 3](https://github.com/OluwafunmilayoB12/Project/blob/main/Visual%203.png)

---

### ROOT CAUSE INSIGHTS

- High vulnerability rate is due to risk constituted in *Critical* and *High* level of severity.
- Payment (150) and User-frontend teams (146) have the highest critical vulnerabilities.
critical vulnerabilities are widespread across the teams, not just a team.
- **2,166 Idle servers** <5% cpu increase server count with no business value and leads to paying for unused resources.
- legacy server dependency representing a major contributor to modernization debt and operational risk.   
- **477 zombie server** still running and incurring cost after decommission date.

---

## RECOMMENDATIONS
* Cloud Operation Lead should immediately terminate 477 zombie servers to reduce the cloud waste by 5-8%.
* CFO & CLoud Operation Lead should implement auto-shutdown and remove 2,166 idle servers.
* Platform Engineering Team should modernize the 4,556 legacy servers to lower modernization debt from 33%.
* Security Engineering Team should prioritize fixing 2,394 Critical and High vulnerabilities to reduce the vulnerability rate below 5%.
* Engineering Managers (All Teams) should take responsibility for resources tagged to their team.

---

### WHY THE FIX WILL WORK

- **Reducing vulnerability rate of 15% to <5%**
20% of Actual server = 2,739 , 90% of risk come from Critical + High vulnerability.
- Critical + High vulnerabities = 2,394(70%).

- Fixing it will reduce the resource vulnerability rate massively.

- **Reducing total vulnerability 3,416 by 60%**
Fixing the Critical and High vul will achieve:
- 70% reduction
- below the target   1366 (3416 * 60%).
- **Automation** prevents recurrence.
- **Immediate terminations** will drive 5–8 % instant savings. 
