# Data provenance

## Purpose

This document records the provenance and redistribution status of inputs used by frozen reference releases and defines provenance expectations for external replication datasets.

No dataset should be added merely because it exists in the development repository. Each reference artifact must have documented provenance, transformation path and redistribution decision.

## Provenance record for reference artifacts

For every dataset or corpus, record:

| Field | Description |
|---|---|
| Artifact ID | Stable identifier used by the release |
| Role | Historical bank, dev set, eval set, normative corpus, auxiliary metadata, etc. |
| Source | Institution, URL or source-system description |
| Acquisition date | When the source was obtained |
| Source version | Version/date when available |
| Transformation | Canonical script or documented processing sequence |
| Source commit | Commit in `gci-nandina-rag` used to derive the artifact |
| Rows/documents | Final cardinality |
| Schema | Relevant fields and types |
| SHA-256 | Frozen artifact hash |
| Redistribution | Allowed / restricted / undecided |
| Reconstruction path | How a third party can regenerate the artifact if it cannot be redistributed |

## Current reference experiment

The source project currently uses the `data_aduanas` Clase 87 `v0.2` split, strategy `T5-safe-159`, as the official basis for new experimental reruns.

This is one **reference instantiation** of the protocol, not a mandatory schema or fixed dataset for external replication.

The reproducibility repository will not copy administrative CSVs until their publication status is explicitly validated. Until then, authoritative copies and manifests remain in the development repository.

## External datasets

For custom runs, users do not need to reproduce the provenance of the reference dataset. They must instead record provenance appropriate to their own data, including at least:

- dataset identifier/version;
- origin or source-system description;
- acquisition/extraction date when known;
- transformation/cleaning steps;
- row count and logical schema mapping;
- hash of the actual file(s) used in the run;
- grouping-unit meaning;
- target-code semantics and level (for example HS-6, NANDINA-8 or another compatible hierarchy);
- restrictions on disclosure or redistribution.

The framework should write these values into the custom-run manifest when supplied through configuration.

## Sensitive or restricted inputs

If a reference input cannot be published:

1. do not commit it here;
2. publish its expected hash and schema when appropriate;
3. provide a reconstruction or placement instruction;
4. make runners fail with a clear message when the input is absent;
5. never substitute synthetic data while claiming reproduction of a frozen reference result.

For custom runs, users are responsible for ensuring that their processing and storage of data comply with applicable permissions and confidentiality requirements.

## Normative sources

Normative documents and derived corpora should separately record:

- original normative source;
- version/date;
- extraction method;
- fragment/document identifiers;
- hierarchy represented;
- corpus hash;
- redistribution/reconstruction conditions.

A custom replication may use a different compatible normative corpus, but its identity and version must be explicit in the run manifest.

## Release requirement

Before the first stable reference release, this document must contain no unresolved provenance item for any artifact required by the canonical reference reproduction command.
