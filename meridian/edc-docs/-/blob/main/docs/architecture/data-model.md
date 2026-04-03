# Data Model

The Enterprise Data Catalog organizes metadata around a set of core entity types connected by typed relationships. This page describes the entity model, its key attributes, and how entities relate to one another.

## Core Entity Types

The EDC's metadata model is built on six primary entity types. Every record in the catalog is an instance of one of these types.

### Data Asset

A **Data Asset** is the foundational entity in the catalog. It represents a discrete, identifiable unit of data — a database table, a flat file, a report, an API endpoint, or a data stream. Every Data Asset carries a unique catalog identifier (EDC-ID) and must be assigned to exactly one Domain and one System of Record.

Key attributes:

| Attribute | Type | Required | Description |
| --- | --- | --- | --- |
| `edc_id` | String | Yes | System-generated unique identifier (e.g., `EDC-00042731`) |
| `name` | String | Yes | Human-readable name of the asset |
| `description` | Text | Yes | Plain-language summary of what the asset contains |
| `asset_type` | Enum | Yes | One of: `Table`, `View`, `File`, `Report`, `API`, `Stream` |
| `system_of_record` | FK → System | Yes | The source system that produces this asset |
| `domain` | FK → Domain | Yes | The functional domain that governs this asset |
| `owner` | FK → Person | Yes | The individual accountable for the asset |
| `steward` | FK → Person | No | The individual responsible for day-to-day metadata quality |
| `classification` | FK → Classification | Yes | The sensitivity classification assigned to this asset |
| `status` | Enum | Yes | One of: `Draft`, `Under Review`, `Published`, `Deprecated`, `Archived` |
| `created_at` | Timestamp | Auto | When the record was created in the catalog |
| `last_reviewed` | Date | No | Date of most recent data quality review |

### Domain

A **Domain** represents a functional area of the business that owns and governs a collection of related Data Assets. Meridian Corp's domain structure mirrors its organizational divisions.

Current domains include: Finance, Human Resources, Supply Chain, Customer Operations, Product Engineering, and Corporate Risk.

Each Domain is led by a **Functional Domain Manager (FDM)** who holds ultimate accountability for the quality and governance of all assets within that domain.

### System

A **System** represents a source application, database, or platform from which Data Assets originate. Systems are registered in the catalog by the IT Architecture team and include connection metadata used by the integration layer.

Examples: `SAP S/4HANA`, `Snowflake DW`, `Salesforce CRM`, `Azure Data Lake (Raw Zone)`.

### Person

A **Person** entity represents an individual who participates in the governance lifecycle. Person records are synchronized nightly from Meridian's Azure Active Directory instance. No manual person creation is permitted.

### Classification

A **Classification** entity defines a sensitivity tier and its associated handling requirements. See [Data Classification](../governance/data-classification.md) for the full classification policy.

### Lineage Link

A **Lineage Link** captures a directional data flow relationship between two Data Assets. Each link records the transformation type (direct copy, aggregation, derivation, join) and the integration mechanism that moves the data (ETL job, API call, manual upload).

## Relationship Model

Entities in the catalog are connected through the following relationships:

```
Domain ──(1:N)──► Data Asset ──(N:1)──► System
                      │
                      ├──(N:1)──► Person (Owner)
                      ├──(N:1)──► Person (Steward)
                      ├──(N:1)──► Classification
                      │
                      └──(N:N)──► Data Asset (via Lineage Link)
```

A Data Asset belongs to exactly one Domain and one System. It must have one Owner and may optionally have a Steward. Every Data Asset must carry a Classification. Lineage Links create a many-to-many graph between Data Assets, enabling end-to-end lineage tracing from source to consumption.

## Attribute Metadata

In addition to the entity-level metadata described above, the EDC supports **column-level** (or field-level) metadata for Data Assets of type `Table`, `View`, and `File`. Each column record captures:

| Attribute | Description |
| --- | --- |
| `column_name` | Physical name of the column |
| `display_name` | Business-friendly name |
| `data_type` | Physical data type (e.g., `VARCHAR(255)`, `INTEGER`, `TIMESTAMP`) |
| `description` | Plain-language description of the column's content |
| `is_pii` | Boolean flag indicating whether the column contains personally identifiable information |
| `glossary_term` | Optional link to a Business Glossary term that defines the concept |

Column metadata can be imported automatically through the schema ingestion connectors described in [Integrations](integrations.md), or entered manually during the asset registration process described in [Registering Data Assets](../sops/registering-data-assets.md).

!!! warning "Schema drift detection"
    When a connector detects that a source system's schema has changed since the last ingestion, the affected Data Asset's status is automatically set to `Under Review` and a notification is sent to the asset's Owner and Steward. Column-level changes are logged in the asset's audit trail.
