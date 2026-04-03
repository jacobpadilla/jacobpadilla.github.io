# Enterprise Data Catalog

Welcome to the documentation site for **Meridian Corp's Enterprise Data Catalog (EDC)** — the centralized platform through which all organizational data assets are registered, classified, governed, and discovered.

## About This Documentation

This site is maintained by the **Data Governance Office (DGO)** and serves as the authoritative reference for anyone who interacts with the Enterprise Data Catalog. It covers three areas:

**System Architecture** describes the technical structure of the EDC platform, including the data model that underpins asset registration, the integration points that connect it to source systems, and the deployment topology that keeps it running.

- [System Overview](architecture/system-overview.md) — Platform components, deployment model, and technology stack.
- [Data Model](architecture/data-model.md) — Entity types, relationship structures, and attribute definitions.
- [Integrations](architecture/integrations.md) — Connectors, ingestion pipelines, and API surface.

**Data Governance** describes the organizational framework that determines how data assets are owned, classified, and reviewed within the catalog.

- [Governance Framework](governance/governance-framework.md) — Principles, organizational structure, and policy hierarchy.
- [Roles & Responsibilities](governance/roles-and-responsibilities.md) — Who does what across the governance lifecycle.
- [Data Classification](governance/data-classification.md) — Sensitivity tiers, labeling rules, and handling requirements.

**Standard Operating Procedures** provide step-by-step instructions for the most common tasks performed within the EDC.

- [Registering Data Assets](sops/registering-data-assets.md) — How to onboard a new table, dataset, or report into the catalog.
- [Data Quality Reviews](sops/conducting-data-reviews.md) — How to plan and execute quarterly data quality reviews.
- [Requesting Access](sops/requesting-access.md) — How to request and approve access to a cataloged asset.
- [Resolving Data Issues](sops/resolving-data-issues.md) — How to log, triage, and close data quality issues.

## Quick Reference

| Item | Value |
| --- | --- |
| EDC Platform Version | 4.2.1 |
| Last Governance Policy Review | 2026-01-15 |
| DGO Contact | dgo@meridian-corp.example.com |
| Change Request Board | Meets every other Thursday, 10:00 AM ET |

!!! note "Keeping this documentation current"
    All documentation in this site is version-controlled in GitLab. If you spot an error or want to suggest an improvement, use the **Edit this page** icon at the top of any page to open the source file directly in the repository.
