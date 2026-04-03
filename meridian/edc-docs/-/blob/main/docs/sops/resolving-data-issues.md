# SOP: Resolving Data Issues

This procedure describes how to log, triage, and close data quality issues discovered in Data Assets registered in the Enterprise Data Catalog. A "data issue" is any defect in the data itself or in its catalog metadata — incorrect values, missing records, stale descriptions, broken lineage, misclassification, or unclear ownership.

## When to Use This Procedure

Use this procedure when:

- You discover incorrect, missing, or inconsistent data in a Data Asset you are consuming.
- A quarterly data quality review produces a **Fail** finding (see [Conducting Data Quality Reviews](conducting-data-reviews.md)).
- An automated quality check or connector alert identifies a problem that requires human investigation.
- You believe an asset is misclassified, incorrectly described, or assigned to the wrong Owner.

!!! note "This procedure is not for access issues"
    If your issue is that you cannot access data you believe you should be able to reach, use the [access request process](requesting-access.md) instead. If you believe an access denial was made in error, contact the Functional Domain Manager directly.

## Issue Severity Levels

All data issues are assigned a severity level that determines triage priority and resolution SLA:

| Severity | Criteria | Resolution SLA |
| --- | --- | --- |
| **Critical** | The issue causes incorrect outputs in a production system, regulatory report, or customer-facing application. Data is being actively consumed in a known-wrong state. | 1 business day (triage within 4 hours) |
| **High** | The issue affects data quality in a significant way but is not yet causing downstream harm — for example, a pipeline is producing incorrect data but no consumer has acted on it yet. | 3 business days |
| **Medium** | The issue degrades metadata quality in the catalog but does not affect the underlying data — for example, a stale description, an outdated Steward assignment, or a missing lineage link. | 10 business days |
| **Low** | The issue is cosmetic or informational — for example, a typo in a column description, a display name that could be improved, or a suggestion for a new glossary term link. | Next quarterly review cycle |

## Procedure

### Step 1 — Log the Issue

Navigate to the affected Data Asset in the EDC. Click the **Report Issue** button on the asset detail page. Complete the issue form:

| Field | Guidance |
| --- | --- |
| **Title** | A concise summary of the problem (e.g., "NULL values in `order_date` column since March 15"). |
| **Severity** | Select the severity level using the criteria above. If you are unsure, select **Medium** — the Steward will adjust during triage. |
| **Category** | Select the issue type: `Data Quality` (incorrect/missing/inconsistent data), `Metadata Quality` (catalog record issues), `Lineage` (incorrect or missing lineage), `Classification` (misclassification), or `Ownership` (incorrect Owner/Steward assignment). |
| **Description** | Describe the issue in enough detail for someone unfamiliar with the asset to understand it. Include: what you observed, what you expected, how you discovered the problem, and any evidence (e.g., a query result showing NULL values, a screenshot of an incorrect lineage path). |
| **Impact** | Describe who is affected and how. Are downstream reports producing wrong numbers? Is a team making decisions based on incorrect data? |
| **Related Review** | If this issue was discovered during a quarterly review, link the review cycle here. |

Click **Submit**. The issue is logged in **Open** status and assigned to the asset's Steward. If no Steward is assigned, the issue is routed to the Functional Domain Manager.

### Step 2 — Triage

The Steward (or FDM) reviews the issue and performs triage:

- **Validate** that the issue is real and reproducible. If the issue cannot be confirmed, the Steward adds a comment explaining why and sets the status to **Cannot Reproduce**. The reporter is notified and can reopen with additional evidence.
- **Adjust severity** if the initial assessment was too high or too low.
- **Assign** the issue to the appropriate resolver. For data quality issues in the source system, this is typically the System Owner. For metadata issues, the Steward resolves it themselves. For classification or ownership issues, the FDM resolves.
- Set the status to **In Progress**.

### Step 3 — Investigate and Resolve

The assigned resolver investigates the root cause and implements a fix. The resolution depends on the issue category:

**Data Quality issues** typically require changes in the source system or the pipeline that produces the asset. The resolver coordinates with the relevant engineering team, validates the fix, and confirms that the corrected data appears in the catalog's next refresh cycle.

**Metadata Quality issues** are resolved by updating the catalog record directly — editing descriptions, fixing column metadata, relinking glossary terms, or updating the review date.

**Lineage issues** require updating or creating Lineage Links in the catalog. The resolver verifies the corrected lineage against the actual data flow (ETL job definition, dbt model, or ADF pipeline).

**Classification issues** require a reclassification proposal, which follows the approval workflow described in [Data Classification — Assigning a Classification](../governance/data-classification.md).

**Ownership issues** require the FDM to reassign the Owner or Steward.

### Step 4 — Verify and Close

After the fix is implemented, the resolver sets the status to **Resolved** and adds a resolution comment describing what was done. The original reporter is notified and has 5 business days to verify the fix.

- If the reporter confirms the fix, the issue moves to **Closed**.
- If the reporter disputes the resolution, the issue is reopened and returned to **In Progress**.
- If the reporter does not respond within 5 business days, the issue is automatically closed.

## Escalation

Issues that are not resolved within their SLA are escalated automatically:

| Stage | Trigger | Action |
| --- | --- | --- |
| First escalation | SLA exceeded by 1 business day | Notification to the FDM (if not already assigned) |
| Second escalation | SLA exceeded by 3 business days | Notification to the DGO |
| Third escalation | SLA exceeded by 5 business days | Issue added to the next DGC meeting agenda |

For **Critical** issues, escalation timelines are compressed: first escalation after 4 hours, second after 8 hours, third after 24 hours.

## Metrics

The DGO tracks the following issue resolution metrics and includes them in the quarterly governance scorecard:

- Total issues opened and closed per domain per quarter.
- Mean time to resolution by severity level.
- Percentage of issues resolved within SLA.
- Recurrence rate (issues reopened or repeated for the same asset).
- Distribution by category (Data Quality vs. Metadata vs. Lineage vs. Classification vs. Ownership).

!!! info "Viewing your issues"
    You can see all issues you have reported or been assigned to under **My Activity → Issues** in the EDC web interface. The DGO also publishes a monthly summary in the `#data-governance` Slack channel.
