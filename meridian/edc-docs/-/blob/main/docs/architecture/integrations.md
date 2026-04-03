# Integrations

The Enterprise Data Catalog connects to Meridian Corp's source systems, identity providers, and downstream tools through a set of managed connectors and APIs. This page describes the integration architecture, available connectors, and the public API surface.

## Integration Architecture

All integrations flow through the EDC's **Integration Gateway**, a dedicated service that handles authentication, rate limiting, payload transformation, and audit logging for both inbound and outbound data flows.

Inbound integrations bring metadata into the catalog from external systems. These include schema crawlers that extract table and column definitions, lineage scanners that trace data movement across ETL pipelines, and identity synchronization jobs that keep the Person registry current.

Outbound integrations push catalog metadata to downstream consumers. These include data quality dashboards, access governance tools, and the corporate data portal.

## Source System Connectors

Each connector is a containerized worker that runs on a configurable schedule. Connectors authenticate to source systems using service accounts whose credentials are stored in Azure Key Vault.

| Connector | Source System | Metadata Extracted | Schedule |
| --- | --- | --- | --- |
| `pg-crawler` | PostgreSQL databases | Schemas, tables, columns, constraints, comments | Daily 01:00 ET |
| `snowflake-crawler` | Snowflake Data Warehouse | Databases, schemas, tables, views, columns, tags | Daily 02:00 ET |
| `sap-extractor` | SAP S/4HANA | Business objects, data elements, domain values | Weekly Sunday 03:00 ET |
| `sfdc-crawler` | Salesforce CRM | Objects, fields, picklist values, record types | Daily 04:00 ET |
| `adls-scanner` | Azure Data Lake Storage | Container/folder structure, file schemas (Parquet, CSV) | Daily 01:30 ET |
| `adf-lineage` | Azure Data Factory | Pipeline definitions, activity mappings, data flows | Every 6 hours |
| `dbt-lineage` | dbt Cloud | Model DAG, source-to-model mappings, test definitions | On dbt run completion (webhook) |

### Connector Lifecycle

Connectors go through four states during each run:

1. **Initializing** — The connector authenticates to the source system and validates its configuration.
2. **Extracting** — Metadata is read from the source and buffered locally.
3. **Reconciling** — Extracted metadata is compared against the catalog's current state. New assets are flagged for creation, changed attributes are staged as updates, and assets no longer present in the source are flagged for review.
4. **Publishing** — Reconciled changes are written to the catalog through the internal Asset Registry API. Each change generates an audit log entry.

Failed connector runs trigger an alert to the `#edc-ops` Slack channel and are automatically retried once after a 15-minute delay. Two consecutive failures escalate to the on-call platform engineer.

## Identity Synchronization

Person records in the catalog are synchronized from Azure Active Directory (AAD) via Microsoft Graph API. The sync job runs nightly at 00:30 ET and performs the following operations:

- Creates Person records for new AAD accounts that belong to Meridian's organizational tenant.
- Updates display name, email, department, and manager attributes for existing records.
- Sets Person records to `Inactive` status when the corresponding AAD account is disabled or deleted. Inactive persons are removed from any active ownership or stewardship assignments, and reassignment notifications are sent to the relevant Functional Domain Manager.

!!! note "Manual person creation is not permitted"
    All Person records must originate from the AAD sync. This ensures that ownership and stewardship assignments always map to verifiable organizational identities.

## Public REST API

The EDC exposes a RESTful API for programmatic interaction with the catalog. The API is versioned and currently supports `v2` endpoints. Authentication is handled via OAuth 2.0 client credentials flow, with tokens issued by Meridian's Azure AD tenant.

### Key Endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/v2/assets` | Search and list Data Assets with filtering and pagination |
| `GET` | `/api/v2/assets/{edc_id}` | Retrieve full metadata for a single Data Asset |
| `POST` | `/api/v2/assets` | Create a new Data Asset (requires `asset:write` scope) |
| `PATCH` | `/api/v2/assets/{edc_id}` | Update attributes on an existing Data Asset |
| `GET` | `/api/v2/assets/{edc_id}/columns` | List column-level metadata for a Data Asset |
| `GET` | `/api/v2/assets/{edc_id}/lineage` | Retrieve upstream and downstream lineage for an asset |
| `GET` | `/api/v2/domains` | List all Domains |
| `GET` | `/api/v2/systems` | List all registered Systems |
| `GET` | `/api/v2/search?q={term}` | Full-text search across all catalog entities |

### Rate Limits

API consumers are subject to the following rate limits per OAuth client:

| Tier | Requests per minute | Burst |
| --- | --- | --- |
| Standard | 60 | 10 |
| Elevated | 300 | 50 |
| Service Account | 1000 | 200 |

Rate limit tier assignment is managed by the DGO. To request an elevated tier, submit a request through the [access request process](../sops/requesting-access.md).

### Bulk Operations

For large-scale metadata imports (e.g., initial catalog population or migrations), the API supports a bulk upload endpoint at `POST /api/v2/bulk/assets` that accepts a JSON array of up to 500 asset records per request. Bulk uploads are processed asynchronously; the response includes a job ID that can be polled for status at `GET /api/v2/bulk/jobs/{job_id}`.

!!! tip "API documentation"
    Interactive API documentation (OpenAPI / Swagger UI) is available at `https://edc.meridian-corp.example.com/api/docs` for authenticated users.
