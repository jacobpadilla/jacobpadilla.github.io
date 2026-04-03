# SOP: Registering Data Assets

This procedure describes how to register a new Data Asset in the Enterprise Data Catalog. Registration is the process of creating a catalog record for a table, file, report, API endpoint, or data stream that is not yet tracked.

## When to Use This Procedure

Use this procedure when:

- A new table or view is created in a source system that is within a connector's crawl scope but has not yet been picked up (e.g., it was created after the last connector run and needs to be registered immediately).
- A new data source is identified that is not covered by an existing connector (e.g., a vendor-provided CSV file, a manually maintained spreadsheet, or a third-party API).
- An existing dataset is being restructured in a way that warrants a new catalog entry rather than an update to the existing record (the FDM makes this determination).

!!! note "Automated registration"
    Most Data Assets are registered automatically through the connector framework described in [Integrations](../architecture/integrations.md). This procedure applies to assets that require manual registration — either because no connector exists for their source system or because the asset needs to be cataloged before the next scheduled connector run.

## Prerequisites

Before beginning registration, confirm the following:

- You have **Steward** or **System Owner** role within the relevant Functional Domain.
- You know which **Domain** the asset belongs to. If uncertain, consult the Functional Domain Manager. The current domain list is maintained in [Governance Framework — Organizational Structure](../governance/governance-framework.md).
- You know which **System** the asset originates from. If the system is not yet registered in the catalog, contact the Catalog Platform Engineer to have it added.
- You have a clear understanding of what the data contains, who produces it, and who consumes it.

## Procedure

### Step 1 — Create the Draft Record

Log in to the EDC web interface at `https://edc.meridian-corp.example.com`. Navigate to **Assets → Register New Asset**. Complete the following required fields:

| Field | Guidance |
| --- | --- |
| **Name** | Use the physical name of the asset as it appears in the source system (e.g., `dim_customer`, `monthly_revenue_report.xlsx`). Do not invent a new name. |
| **Asset Type** | Select the type that best matches: `Table`, `View`, `File`, `Report`, `API`, or `Stream`. |
| **Domain** | Select the Functional Domain that governs this data. |
| **System of Record** | Select the source system where this data is authoritatively produced. |
| **Description** | Write a plain-language summary of what this asset contains, why it exists, and who its primary audience is. Aim for 2–4 sentences. Avoid jargon that would be unintelligible to someone outside your team. |

The record is saved in **Draft** status. No approval is required to create a draft.

### Step 2 — Add Column Metadata

If the asset is of type `Table`, `View`, or `File`, navigate to the **Columns** tab and add column-level metadata. For each column, provide:

- **Column Name** — The physical column name as it appears in the source.
- **Display Name** — A business-friendly name (e.g., `cust_id` → `Customer Identifier`).
- **Data Type** — The physical data type.
- **Description** — What this column contains in plain language.
- **PII Flag** — Set to `Yes` if the column contains personally identifiable information.
- **Glossary Term** — If a matching term exists in the Business Glossary, link it here.

!!! tip "Importing columns from DDL"
    If you have a DDL script or a sample file, you can use the **Import Columns** button to parse column names and data types automatically. You will still need to add descriptions, PII flags, and glossary links manually.

### Step 3 — Assign Classification

Navigate to the **Classification** tab and assign a sensitivity tier based on the criteria defined in [Data Classification](../governance/data-classification.md). Remember: the classification should reflect the **most sensitive element** in the asset. If the asset contains any PII, the minimum classification is **Confidential**; if it contains regulated PII (GDPR, CCPA, PHI, PCI), it must be **Restricted**.

Document your classification rationale in the **Classification Notes** field. This note will be visible to the FDM during approval.

### Step 4 — Assign Ownership

Navigate to the **Ownership** tab and assign:

- **Owner** — The individual accountable for this asset. This is typically a manager or senior IC in the business area that produces or consumes the data.
- **Steward** (optional but recommended) — The individual responsible for ongoing metadata quality. This may be you.

Both individuals must exist as active Person records in the catalog (synced from Azure AD).

### Step 5 — Add Lineage (If Known)

If you know how this asset is produced or where it flows downstream, navigate to the **Lineage** tab and add upstream and/or downstream links. For each link, specify:

- The related Data Asset (search by name or EDC-ID).
- The direction (upstream = data flows into this asset; downstream = data flows out).
- The transformation type (`Direct Copy`, `Aggregation`, `Derivation`, `Join`).
- The integration mechanism (`ETL Job`, `API Call`, `Manual Upload`, `dbt Model`).

Lineage is optional at registration time but will be required before the asset's first quarterly review.

### Step 6 — Submit for Review

When all required fields are complete, click **Submit for Review**. This transitions the asset from **Draft** to **Under Review** and triggers a notification to the Functional Domain Manager for the selected domain.

The FDM will review the record for completeness, classification accuracy, and appropriate ownership. The FDM may:

- **Approve** — The asset moves to **Published** status and becomes discoverable in catalog search.
- **Request Changes** — The asset returns to **Draft** with reviewer comments. Address the comments and resubmit.
- **Reject** — The asset is rejected with a rationale (e.g., duplicate record, out-of-scope asset). Rejected records remain in the catalog in **Rejected** status for audit purposes.

## SLA

The FDM should complete their review within **3 business days** of submission. If the review is not completed within this window, the DGO will follow up with the FDM.

## Post-Registration

Once an asset is Published:

- It appears in catalog search results and is browsable by all Meridian employees.
- It will be included in the next quarterly data quality review cycle for its domain.
- The Classification Engine will evaluate propagation rules for any Lineage Links.
- If the asset's source system has a connector, the connector will begin reconciling the manual record with its automated crawl results on the next scheduled run.
