# Data provenance

## Purpose

This document will record the provenance and redistribution status of every input required by a reproducibility release.

No dataset should be added to this repository merely because it exists in the development repository. Each artifact must have a documented provenance, transformation path and redistribution decision.

## Provenance record required for each artifact

For every dataset or corpus, record:

| Field | Description |
|---|---|
| Artifact ID | Stable identifier used by the release |
| Role | Historical bank, dev set, eval set, normative corpus, auxiliary metadata, etc. |
| Source | Institution, URL or source-system description |
| Acquisition date | When the source was obtained |
| Source version | Version/date of the source when available |
| Transformation | Canonical script or documented processing sequence |
| Source commit | Commit in `gci-nandina-rag` used to derive the artifact |
| Rows/documents | Final cardinality |
| Schema | Relevant fields and types |
| SHA-256 | Frozen artifact hash |
| Redistribution | Allowed / restricted / undecided |
| Reconstruction path | How a third party can regenerate the artifact if it cannot be redistributed |

## Current approved split reference

The source project currently uses the `data_aduanas` Clase 87 `v0.2` split, strategy `T5-safe-159`, as the official basis for new experimental reruns.

The reproducibility repository will not copy those CSVs until the data-publication decision is explicitly validated. Until then, their authoritative copies and manifests remain in the development repository.

## Sensitive or restricted inputs

If an input cannot be published:

1. do not commit it here;
2. publish its expected hash and schema when appropriate;
3. provide a reconstruction or placement instruction;
4. make runners fail with a clear message when the input is absent;
5. never substitute synthetic data while claiming reproduction of the thesis result.

## Normative sources

Normative documents and derived corpora must separately record:

- original normative source;
- version/date;
- extraction method;
- fragment/document identifiers;
- hierarchy represented;
- corpus hash;
- whether the source text itself may be redistributed or must be rebuilt from public sources.

## Final release requirement

Before the first thesis release, this document must contain no unresolved provenance item for any artifact required by `scripts/reproduce_all.py`.
