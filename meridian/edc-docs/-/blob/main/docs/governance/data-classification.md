# Data Classification

All Data Assets registered in the Enterprise Data Catalog must be assigned a classification that reflects the sensitivity of the data they contain. This page defines Meridian Corp's classification tiers, the rules for assigning them, and the handling requirements associated with each tier.

## Classification Tiers

Meridian uses a four-tier classification model. Every Data Asset must carry exactly one classification. When in doubt, the higher (more restrictive) classification applies.

### Public

Data that is intended for unrestricted distribution or that has already been made publicly available by Meridian. No harm would result from unauthorized disclosure.

**Examples:** Published press releases, public financial filings, marketing materials, product documentation.

**Handling requirements:**

- No access restrictions within the catalog.
- May be shared externally without approval.
- No encryption requirements beyond standard transport-layer security.

### Internal

Data that is intended for use within Meridian Corp and would cause minimal harm if disclosed outside the organization. This is the default classification for most operational data.

**Examples:** Internal project plans, non-sensitive operational reports, org charts, meeting notes, internal knowledge base content.

**Handling requirements:**

- Accessible to all Meridian employees through standard catalog discovery.
- Must not be shared externally without manager approval.
- Must be stored on Meridian-managed infrastructure.

### Confidential

Data whose unauthorized disclosure could cause significant harm to Meridian, its partners, or its customers. This tier includes most business-critical data that is not regulated.

**Examples:** Revenue figures prior to earnings release, strategic plans, vendor contract terms, salary bands, non-public customer analytics, source code.

**Handling requirements:**

- Access requires explicit approval from the Data Asset Owner or FDM (see [Requesting Access](../sops/requesting-access.md)).
- Must be encrypted at rest using AES-256 or equivalent.
- Sharing with external parties requires Legal review and a signed NDA.
- Must not be stored on personal devices or in non-approved cloud services.
- Data extracts must be logged and are subject to quarterly audit.

### Restricted

Data whose unauthorized disclosure would cause severe harm, including regulatory penalties, legal liability, or significant reputational damage. This tier covers regulated data and the most sensitive business information.

**Examples:** Personally identifiable information (PII) governed by GDPR or CCPA, protected health information (PHI), payment card data (PCI-DSS scope), trade secrets, M&A planning documents, board materials prior to public disclosure.

**Handling requirements:**

- Access requires approval from both the Data Asset Owner and the relevant FDM, with justification documented.
- Must be encrypted at rest and in transit. Key management must use Meridian's centralized Azure Key Vault instance.
- Column-level access controls must be applied where the underlying system supports them. Broad table-level grants are not acceptable for Restricted assets.
- All access is time-bounded; grants expire after 90 days and must be re-approved.
- Cross-border transfers require a Data Transfer Impact Assessment approved by Legal and Compliance.
- Must not be included in development or staging environments without anonymization or pseudonymization.
- Bulk extracts are prohibited except through an approved and audited pipeline.

## Assigning a Classification

Classification is assigned during the asset registration process (see [Registering Data Assets](../sops/registering-data-assets.md)) and must be approved by the Functional Domain Manager before the asset is published to the catalog.

The classification decision should be based on the **most sensitive data element** present in the asset. If a table contains 50 columns of Internal data and one column of Restricted PII, the entire asset is classified as Restricted.

!!! tip "Column-level classification"
    While the overall asset classification is driven by the most sensitive element, Stewards are encouraged to tag individual columns with their own sensitivity level using the `is_pii` flag and the `sensitivity` column attribute. This enables downstream systems to apply fine-grained access controls and masking rules.

## Classification Propagation

When data flows from one asset to another — captured by Lineage Links in the catalog — classification propagates according to the following rules:

1. **Direct copy or replication:** The downstream asset inherits the classification of the upstream asset.
2. **Aggregation:** If the aggregation removes all Restricted or Confidential elements (e.g., averaging salaries across 1,000+ employees with no individual attribution), the downstream asset may be classified at a lower tier. This downgrade requires explicit FDM approval and documentation of the reasoning.
3. **Join or enrichment:** The downstream asset takes the higher classification of the inputs. If a Confidential dataset is joined with a Restricted dataset, the output is Restricted.
4. **Derivation with filtering:** If sensitive columns are excluded from the derivation, the downstream asset may qualify for a lower classification. The Steward must document which columns were excluded and the FDM must approve.

The EDC's Classification Engine evaluates these rules automatically when new Lineage Links are created and flags assets whose current classification may be inconsistent with their lineage.

## Periodic Review

All classification assignments are reviewed as part of the quarterly data quality review cycle. During the review, the Steward and FDM assess whether:

- The data content has changed in a way that affects sensitivity (e.g., a new PII column was added).
- Regulatory requirements have changed.
- The asset's usage pattern has shifted (e.g., data previously used only for internal analysis is now shared with a partner).

Classification changes follow the same approval workflow as initial classification: the Steward proposes, the FDM approves. Reclassification from a lower tier to a higher tier takes effect immediately. Reclassification from a higher tier to a lower tier takes effect after a 5-business-day waiting period to allow downstream consumers to adjust.
