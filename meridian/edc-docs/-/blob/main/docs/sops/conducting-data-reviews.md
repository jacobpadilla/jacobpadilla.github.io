# SOP: Conducting Data Quality Reviews

This procedure describes how to plan and execute the quarterly data quality reviews that are required for all Published Data Assets in the Enterprise Data Catalog.

## Purpose

Quarterly reviews ensure that catalog metadata remains accurate, complete, and aligned with the current state of the underlying data. Reviews catch problems that automated processes cannot detect: descriptions that have drifted out of date, ownership assignments that no longer reflect organizational reality, classification decisions that need revision, and lineage gaps.

## Review Schedule

Data quality reviews are conducted on a quarterly cycle aligned with the fiscal calendar:

| Review Period | Scope | Due Date |
| --- | --- | --- |
| Q1 | All Published assets as of Jan 1 | February 28 |
| Q2 | All Published assets as of Apr 1 | May 31 |
| Q3 | All Published assets as of Jul 1 | August 31 |
| Q4 | All Published assets as of Oct 1 | November 30 |

The DGO generates the review worklist for each domain at the start of the review period and distributes it to the Functional Domain Manager.

## Roles in the Review Process

**Functional Domain Manager (FDM):** Owns the review for the domain. Distributes review assignments, monitors progress, reviews completed assessments, and signs off on the domain's final review report.

**Data Steward:** Performs the review for each assigned Data Asset. Evaluates metadata quality, documents findings, and proposes corrective actions.

**System Owner:** Consulted for technical accuracy questions. Validates schema metadata and lineage when the Steward raises concerns.

**DGO:** Produces the review worklist, tracks completion across all domains, and compiles the enterprise-wide quality scorecard.

## Procedure

### Step 1 — Receive and Distribute the Worklist

At the start of each review period, the DGO generates a worklist for each domain containing all Published assets. The worklist is delivered to the FDM as a filtered view in the EDC under **Reviews → Current Cycle**. Each worklist entry shows the asset name, EDC-ID, current Owner, current Steward, classification, and the date of the last review.

The FDM distributes review assignments to Stewards. If an asset has an assigned Steward, that Steward conducts the review. If no Steward is assigned, the FDM either assigns one or conducts the review themselves.

### Step 2 — Evaluate Each Asset

For each assigned asset, the Steward opens the asset record in the catalog and evaluates the following dimensions:

**Completeness** — Are all required fields populated? Is the description substantive (not just a restatement of the table name)? Are column-level descriptions present for all columns? Is a classification assigned and documented?

**Accuracy** — Does the description still accurately reflect what the data contains? Has the schema changed since the last review (check the audit trail for schema drift alerts)? Are the Owner and Steward still the correct individuals? Is the classification still appropriate given the current data content?

**Currency** — Is the asset still actively produced and consumed? If the asset has not been updated in the source system in over 90 days, should it be deprecated? Are the Lineage Links still valid?

**Conformity** — Does the asset comply with Tier 2 Domain Policies? For example, Finance assets must have a currency code column; HR assets must have a `data_subject_category` tag.

### Step 3 — Document Findings

For each asset, the Steward records a review outcome in the EDC:

| Outcome | Meaning |
| --- | --- |
| **Pass** | The asset's metadata meets quality standards. No action required. |
| **Pass with Observations** | The asset's metadata is acceptable but improvements are recommended. Observations are logged as informational notes. |
| **Fail — Corrective Action Required** | One or more quality dimensions are deficient. A corrective action item is created and assigned. |
| **Recommend Deprecation** | The asset appears to be obsolete or redundant. The FDM will decide whether to deprecate. |

For each **Fail** outcome, the Steward creates an issue through the [data issue resolution process](resolving-data-issues.md), linking it to the review cycle.

### Step 4 — FDM Review and Sign-Off

After all Stewards have completed their assignments, the FDM reviews the aggregated results in the **Reviews → Current Cycle** dashboard. The FDM:

- Verifies that all assets in the domain have been reviewed.
- Reviews any **Fail** findings and confirms that corrective action items are appropriate and assigned.
- Acts on **Recommend Deprecation** findings by either approving the deprecation (moving the asset to **Deprecated** status) or overriding the recommendation with a documented rationale.
- Signs off on the domain review by clicking **Complete Domain Review** in the dashboard.

### Step 5 — Enterprise Scorecard

After all domain reviews are complete, the DGO compiles an enterprise-wide data quality scorecard that reports:

- Pass rate by domain (percentage of assets that passed without issues).
- Number and severity of corrective action items by domain.
- Number of assets recommended for deprecation.
- Trend comparison against the prior quarter.

The scorecard is presented to the Data Governance Council at the next monthly meeting.

## Handling Overdue Reviews

If a domain's review is not completed by the due date:

1. The DGO sends a reminder to the FDM on the due date.
2. If the review is still incomplete 5 business days after the due date, the DGO escalates to the CDO.
3. Assets that are not reviewed by the end of the escalation period are flagged with a **Review Overdue** badge in the catalog, visible to all users.

!!! warning "Review is mandatory"
    Quarterly reviews are a Tier 1 governance requirement. A domain's review pass rate is included in the FDM's performance objectives and in the governance metrics reported to the executive team.
