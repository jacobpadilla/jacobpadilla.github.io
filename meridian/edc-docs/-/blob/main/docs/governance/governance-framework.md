# Governance Framework

This page describes the principles, organizational structure, and policy hierarchy that guide how Meridian Corp governs the data assets registered in the Enterprise Data Catalog.

## Guiding Principles

Meridian Corp's data governance program is built on five principles that inform every policy, procedure, and design decision related to the EDC.

**Accountability is personal.** Every Data Asset in the catalog must have a named Owner — an individual, not a team or a role. Ownership can be delegated operationally through Stewards, but accountability cannot be diffused.

**Metadata is a product.** Catalog metadata is treated with the same rigor as production data. It has quality standards, review cycles, and SLAs. Incomplete or stale metadata is considered a defect.

**Governance follows the data.** Classification and handling rules travel with the data as it moves through pipelines. If a Confidential table is joined into a downstream report, that report inherits the Confidential classification unless the sensitive columns are explicitly excluded and the exclusion is verified.

**Transparency over gatekeeping.** The default posture is to make data discoverable. Access controls restrict who can use the data, not who can know it exists. Every Meridian employee can browse the catalog and see what assets are available, even if they lack permission to query the underlying data.

**Automation where possible, judgment where necessary.** Schema ingestion, lineage tracing, and classification propagation are automated. Ownership assignment, exception approvals, and dispute resolution require human judgment and are routed through defined workflows.

## Organizational Structure

Data governance at Meridian is organized into a three-level hierarchy anchored by Functional Domains.

### Data Governance Council (DGC)

The **Data Governance Council** is the executive body that sets policy direction, approves classification standards, and resolves cross-domain disputes. It meets monthly and is chaired by the Chief Data Officer (CDO). Membership includes all Functional Domain Managers and representatives from Legal, Compliance, Information Security, and IT Architecture.

The DGC does not make operational decisions about individual Data Assets. Its role is to establish the rules of the road and arbitrate when those rules produce conflicting outcomes.

### Data Governance Office (DGO)

The **Data Governance Office** is the operational arm of the governance program. The DGO is responsible for maintaining this documentation, administering the EDC platform, training new users, producing governance metrics, and supporting Functional Domain Managers in their day-to-day governance work.

The DGO reports to the CDO and currently consists of four staff members: a Governance Program Manager, two Data Governance Analysts, and a Catalog Platform Engineer.

### Functional Domains

A **Functional Domain** is a business-aligned grouping of Data Assets that share a common governance context. Each domain is led by a **Functional Domain Manager (FDM)** — a senior individual contributor or manager who understands the business meaning of the data and has the authority to make governance decisions within their scope.

Within each domain, **System Owners** are responsible for the technical accuracy of metadata for assets originating from a specific source system. **Domain Staff** — the analysts, engineers, and business users who work with the data daily — are responsible for flagging issues, participating in data quality reviews, and keeping asset descriptions current.

For a detailed breakdown of each role's responsibilities, see [Roles & Responsibilities](roles-and-responsibilities.md).

## Policy Hierarchy

Governance policies are organized into three tiers, each with a different approval authority and change cadence:

### Tier 1 — Enterprise Standards

Enterprise Standards are organization-wide policies approved by the Data Governance Council. They apply to all domains and all systems. Changes require a formal proposal, a 30-day comment period, and a DGC vote.

Current Tier 1 standards:

- Data Classification Standard (see [Data Classification](data-classification.md))
- Data Retention and Disposal Standard
- Cross-Border Data Transfer Standard

### Tier 2 — Domain Policies

Domain Policies are created and maintained by individual Functional Domain Managers. They must be consistent with Tier 1 standards but may add domain-specific requirements. For example, the Finance domain requires that all Data Assets with monetary values include a currency code column, while the HR domain requires that assets containing employee data carry a `data_subject_category` tag.

Domain Policies are approved by the relevant FDM and reviewed annually.

### Tier 3 — Operating Procedures

Operating Procedures (SOPs) describe how to perform specific tasks within the catalog. They are maintained by the DGO and the responsible domain staff. SOPs do not require DGC approval but must be consistent with Tier 1 and Tier 2 policies. The [Standard Operating Procedures](../sops/registering-data-assets.md) section of this site contains the current set of SOPs.

## Compliance Mapping

The governance framework is designed to support Meridian's regulatory obligations. The following table maps governance capabilities to the regulatory requirements they address:

| Regulatory Requirement | Governance Capability | EDC Feature |
| --- | --- | --- |
| GDPR Art. 30 — Records of processing activities | Asset registration with processing purpose | Data Asset `purpose` attribute |
| GDPR Art. 35 — Data protection impact assessment | Classification and sensitivity labeling | Classification entity and propagation rules |
| SOX Section 404 — Internal controls over financial reporting | Ownership accountability, review cycles | Owner/Steward assignments, quarterly reviews |
| CCPA — Right to know / Right to delete | Data lineage and PII flagging | Lineage Links, `is_pii` column attribute |

!!! info "Policy documents"
    Full policy text for Tier 1 standards is maintained in the `edc-policies` GitLab repository and published to Meridian's internal policy portal. This documentation describes the framework; the authoritative policy text lives in that repository.
