# Data contract

## Purpose

The framework operates on logical fields and an explicit tariff-classification contract. It must not assume fixed source-column names, a fixed chapter, NANDINA-only labels, or a fixed code length.

A user-provided dataset is compatible when its columns can be mapped to the required logical roles and its labels are consistent with the configured classification hierarchy.

## Required logical fields

| Logical field | Required | Meaning |
|---|---:|---|
| `analysis_id` | Yes | Unique identifier of the evaluated instance |
| `description` | Yes | Commercial/product text used as retrieval query |
| `label` | Yes | Reference tariff code at the configured target level |
| `group_id` | Recommended | Higher-level unit that must remain within one split when instances are dependent |

Example mapping:

```yaml
dataset:
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: tariff_code
```

## Classification contract

Every run must declare the tariff hierarchy explicitly.

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]
```

The framework may therefore evaluate HS-2, HS-4, HS-6, regional extensions such as NANDINA when applicable, national 8/10-digit extensions, or another compatible hierarchical tariff nomenclature.

`target_digits` defines the level at which the ground-truth label and primary ranking metrics are evaluated. `hierarchy_digits` defines the levels available for validation and hierarchical error analysis.

## Experimental scope

Chapter filtering is optional:

```yaml
scope:
  chapters: null
```

`null` means all chapters represented in the supplied dataset. One or several chapters may be selected when the research design requires a narrower experimental scope. Such filtering is a study-level choice, not a framework constraint.

## Optional logical fields

The framework may consume or derive:

- chapter / HS-2;
- heading / HS-4;
- HS-6;
- regional extension;
- national extension;
- timestamp/period;
- source identifier;
- pre-existing split label;
- auxiliary provenance fields.

Optional fields must never silently become query features unless explicitly enabled by the experimental configuration.

## Core integrity checks

A dataset validator should fail or warn explicitly for:

- missing required columns;
- duplicate `analysis_id` values;
- empty descriptions;
- labels whose length/format is incompatible with `target_digits`;
- labels incompatible with the configured jurisdiction/nomenclature when a validation source is available;
- inconsistent hierarchy prefixes (for example HS-4 not matching the first four digits of a 10-digit label);
- invalid or missing `group_id` when grouped splitting is requested;
- the same `group_id` assigned to multiple splits;
- the same `analysis_id` assigned to multiple splits;
- rows excluded by configured scope filters without explicit counts;
- unsupported encodings or non-deterministic serialization for frozen reference presets.

## Group dependence

If multiple rows derive from the same declaration, transaction, document, shipment or another higher-level source, that dependency should be represented through `group_id`.

When grouped partitioning is enabled:

```text
one group_id -> exactly one partition
```

This prevents historical/evaluation dependence caused by distributing related instances across splits.

## Normative compatibility contract

When normative retrieval or controlled explanation is enabled, the run must also declare the normative corpus and its coverage:

```yaml
normative:
  enabled: true
  corpus_path: data/normative/chile_2026/
  jurisdiction: CL
  nomenclature: national_tariff
  supported_digits: [2, 4, 6, 8, 10]
```

The validator must compare `classification.target_digits` with `normative.supported_digits`.

A target level deeper than the normative corpus is not necessarily forbidden, but it must be explicitly reported. Example:

```text
target classification level: 10 digits
normative evidence coverage: 8 digits
national-extension evidence: unavailable
```

The explanation layer must never imply normative support at a deeper level than the supplied corpus actually provides.

See [`TAXONOMY_AND_NORMATIVE_CORPUS.md`](TAXONOMY_AND_NORMATIVE_CORPUS.md).

## Query isolation

The retrieval query must not include the reference label or fields derived from it. By default, prohibited query features include:

- true target code;
- hierarchy derived from the true label;
- evaluation outcome;
- split membership as semantic content;
- hidden identifiers that encode the label.

## Split inputs

Two custom-data modes should be supported.

### Pre-split

The user supplies historical/dev/eval partitions. The framework validates independence, hierarchy compatibility and coverage but does not silently repartition them.

### Unsplit

The user supplies one dataset plus a split policy. The framework constructs deterministic grouped partitions and records assignments in the run manifest.

## Reproducibility metadata

Every custom run should record:

- dataset path(s) and SHA-256;
- row and group counts;
- logical column mapping;
- classification family, jurisdiction and nomenclature;
- target and hierarchy levels;
- selected chapter scope or `all`;
- grouping-unit definition;
- split policy and exact partition counts;
- duplicate/dependence audit results;
- normative corpus identity, version, coverage and SHA-256;
- configuration hash;
- framework commit/version.

## Synthetic example

A stable release should include a small synthetic dataset spanning multiple tariff chapters and hierarchy levels so the generic interface can be tested without access to administrative data.
