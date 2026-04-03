# System Overview

The Enterprise Data Catalog (EDC) is a centralized metadata management platform that provides Meridian Corp with a single point of reference for all organizational data assets. This page describes the platform's components, deployment model, and technology stack.

## Platform Architecture

The EDC is deployed as a three-tier application across Meridian's private cloud infrastructure. Each tier is isolated within its own network segment and communicates through well-defined API boundaries.

**Presentation Tier** — A browser-based web application that serves as the primary interface for all catalog users. The UI provides asset search and discovery, lineage visualization, glossary browsing, and workflow management. It is built on a React frontend served by an NGINX reverse proxy.

**Application Tier** — A set of RESTful microservices responsible for business logic, workflow orchestration, and integration coordination. Core services include the Asset Registry Service, the Classification Engine, the Lineage Processor, and the Workflow Manager. These services are deployed as containerized workloads managed by Kubernetes.

**Persistence Tier** — The catalog's metadata is stored across two backing stores. A PostgreSQL 16 relational database holds structured metadata such as asset definitions, ownership records, classification labels, and workflow state. An OpenSearch cluster provides full-text indexing for asset discovery and search.

## Deployment Topology

The EDC operates across three environments, each serving a distinct purpose in the delivery lifecycle:

| Environment | Purpose | Refresh Cadence |
| --- | --- | --- |
| DEV | Feature development and unit testing | Continuous |
| STAGING | Integration testing and user acceptance | Weekly from DEV |
| PROD | Live catalog used by all Meridian staff | Bi-weekly release windows |

All environments are provisioned on Azure virtual machines running Rocky Linux 9. Configuration management is handled by Ansible, and infrastructure definitions are maintained as Terraform modules in the `edc-infra` GitLab repository.

## Technology Stack

The following components make up the EDC's runtime stack:

| Component | Technology | Version |
| --- | --- | --- |
| Web Server | NGINX | 1.26 |
| Frontend | React / TypeScript | 19.x |
| API Gateway | Kong | 3.6 |
| Application Services | Python / FastAPI | 3.12 / 0.111 |
| Relational Database | PostgreSQL | 16.2 |
| Search Index | OpenSearch | 2.13 |
| Container Runtime | Podman | 5.x |
| Orchestration | Kubernetes (AKS) | 1.29 |
| CI/CD | GitLab CI | 17.x |

## High Availability

The PROD environment is configured for high availability across two Azure availability zones. The PostgreSQL database uses synchronous streaming replication with automatic failover managed by Patroni. The OpenSearch cluster runs three data nodes with a replication factor of two. The Kubernetes deployment maintains a minimum of two replicas for each application service.

Scheduled maintenance windows occur on the second Saturday of each month from 02:00–06:00 ET. Maintenance that falls outside these windows requires approval from the Change Request Board.

!!! info "Related pages"
    - [Data Model](data-model.md) — Entity types and relationships within the catalog's persistence layer.
    - [Integrations](integrations.md) — How external systems connect to the EDC.
