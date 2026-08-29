# Data and corpus provenance

## Purpose

This document defines provenance requirements for the reference experimental evidence and for external replications. It does not imply that restricted datasets or corpora are redistributed through this repository.

## Provenance record

For every dataset or normative corpus used in a frozen reference record or external replication, document:

| Field | Description |
|---|---|
| Artifact ID | Stable identifier |
| Role | Historical bank, dev/eval set, normative corpus, auxiliary metadata, etc. |
| Source | Institution, URL or source-system description |
| Jurisdiction | International, regional or national scope when applicable |
| Acquisition date | When the source was obtained |
| Source version | Version/date/year when available |
| Transformation | Published transformation specification or documented processing sequence |
| Rows/documents | Final cardinality |
| Schema | Relevant logical fields and types |
| Taxonomy coverage | HS/tariff hierarchy represented |
| SHA-256 | Frozen hash when the artifact is shareable or independently available |
| Redistribution | Allowed / restricted / undecided |
| Reconstruction path | How a third party may obtain or rebuild it when redistribution is not allowed |

## Reference evidence

Reference-specific dataset choices belong under `reference_experiment/`. A restricted chapter, jurisdiction or code depth used by one campaign is not a universal protocol requirement.

Administrative data should not be copied into this public repository unless publication rights are explicitly validated. Where data cannot be redistributed, publish the schema, provenance, transformation specification, expected hash where appropriate, and sufficient aggregate metadata to describe the experimental sample without disclosing restricted records.

## External replication datasets

Replicators should document their own data provenance, including:

- dataset identifier/version;
- origin/source-system description;
- extraction/acquisition date;
- transformation/cleaning procedure;
- row counts and logical-field mapping;
- file hashes where feasible;
- grouping-unit semantics;
- target nomenclature and depth;
- confidentiality/redistribution restrictions.

## Normative corpus provenance

For every normative layer, record:

- original normative source;
- jurisdiction;
- nomenclature/version/year;
- hierarchy levels covered;
- extraction/fragmentation specification;
- document or fragment identifiers;
- corpus/build hash where feasible;
- redistribution or reconstruction conditions.

A replication using a national 10-digit target should identify the national normative material required to ground that depth. If only a parent level is documented, the gap must be explicit.

## Restricted inputs

If an artifact cannot be published:

1. do not commit it;
2. publish its logical schema and provenance where permitted;
3. publish a reconstruction/placement specification if possible;
4. publish expected hashes only when doing so is appropriate;
5. never substitute synthetic data while claiming it represents reference evidence.

Synthetic examples belong under `examples/synthetic/` and are strictly demonstrative.