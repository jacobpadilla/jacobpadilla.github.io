# SOP: Requesting Access

This procedure describes how to request access to a Data Asset that is registered in the Enterprise Data Catalog. Access requests apply to the **underlying data** — all Meridian employees can browse the catalog and view metadata without a request, but querying, downloading, or connecting to the actual data requires an approved access grant.

## When to Use This Procedure

Use this procedure when you need to:

- Query a database table or view that you do not currently have permissions for.
- Access files in a restricted storage container (e.g., an Azure Data Lake zone classified as Confidential or Restricted).
- Connect a BI tool, notebook, or application to a data source through the EDC's access gateway.
- Obtain an API credential scoped to a specific set of Data Assets.

!!! note "Public and Internal assets"
    For assets classified as **Public** or **Internal**, access is typically provisioned automatically through Meridian's standard role-based access control (RBAC). If you are unable to access a Public or Internal asset, contact the Catalog Platform Engineer — the issue is likely a misconfigured permission rather than a missing access grant.

## Prerequisites

- You must have an active Meridian employee account (synchronized to the catalog as a Person record).
- You must be able to articulate a business justification for the access — what you will use the data for and why you need it.
- You should review the asset's classification and handling requirements in [Data Classification](../governance/data-classification.md) before submitting your request, as higher classifications carry additional obligations.

## Procedure

### Step 1 — Locate the Asset

Search for the Data Asset in the EDC web interface using the search bar or by browsing the relevant Domain. Open the asset's detail page and confirm that it is the dataset you need by reviewing its description, column metadata, and lineage.

### Step 2 — Submit an Access Request

Click the **Request Access** button on the asset detail page. Complete the access request form:

| Field | Guidance |
| --- | --- |
| **Access Type** | Select the level of access needed: `Read Only`, `Read + Export`, or `Read + Write`. Most analytical use cases require `Read Only`. |
| **Duration** | Select the requested grant duration: `30 days`, `90 days`, `180 days`, or `Ongoing`. Note that **Restricted** assets are capped at 90 days regardless of what you select. |
| **Business Justification** | Explain in 2–3 sentences why you need this data, what project or analysis it supports, and what deliverable it contributes to. Vague justifications (e.g., "general analysis") will be returned for clarification. |
| **Access Method** | Indicate how you will access the data: `Direct SQL`, `BI Tool (specify)`, `Python/Notebook`, `API`, or `File Download`. |
| **Data Handling Acknowledgment** | For Confidential and Restricted assets, you must check the box confirming that you have read and will comply with the handling requirements for the asset's classification tier. |

Click **Submit**. The request enters the approval workflow.

### Step 3 — Approval Workflow

Access requests are routed based on the asset's classification:

**Internal assets:** Approved by the Data Asset Owner. If the Owner does not act within 2 business days, the request is escalated to the FDM.

**Confidential assets:** Approved by the Data Asset Owner. The Owner may consult the Steward before deciding. SLA: 3 business days.

**Restricted assets:** Requires sequential approval from the Data Asset Owner and the Functional Domain Manager. Both must approve for the grant to be provisioned. SLA: 5 business days.

At any stage, the approver may:

- **Approve** the request as submitted.
- **Approve with conditions** — for example, approving Read Only when Read + Export was requested, or reducing the grant duration.
- **Request clarification** — the request is returned to the requester with a comment.
- **Deny** — the request is denied with a documented rationale.

### Step 4 — Provisioning

Once all required approvals are obtained, the access grant is provisioned automatically by the EDC's Access Provisioning Service. Provisioning typically completes within 15 minutes. You will receive an email notification with:

- Confirmation that access has been granted.
- The access method and connection details (e.g., database host, schema, role name).
- The expiration date of the grant.
- A reminder of the handling requirements for the asset's classification.

### Step 5 — Expiration and Renewal

Access grants expire on the date specified during provisioning. The EDC sends a reminder email 7 days before expiration. To renew:

- Navigate to **My Access → Active Grants** in the EDC.
- Click **Renew** next to the expiring grant.
- The renewal follows the same approval workflow as the original request. You do not need to restate the justification unless your use case has changed.

If a grant expires without renewal, access is revoked automatically. There is no grace period.

## Emergency Access

In situations where data access is urgently needed and the standard approval workflow cannot be completed in time (e.g., a production incident, a regulatory inquiry with a same-day deadline), the following expedited process applies:

1. Contact the FDM for the relevant domain directly (email, Slack, or phone).
2. The FDM may grant **temporary emergency access** for up to 48 hours by logging an override in the EDC.
3. The standard access request must be submitted retroactively within 24 hours of the emergency grant.
4. Emergency access grants are flagged in the quarterly audit report and reviewed by the DGC.

!!! warning "Emergency access is audited"
    Emergency grants are intended for genuine operational urgency, not for convenience. Patterns of repeated emergency access by the same individual or for the same asset will be investigated by the DGO.
