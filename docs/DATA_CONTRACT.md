# Data contract

## Purpose

The framework must operate on logical fields rather than fixed source-column names. A user-provided dataset is compatible when its columns can be mapped to the required logical roles and pass the integrity checks defined here.

## Required logical fields

| Logical field | Required | Meaning |
|---|---:|---|
| `analysis_id` | Yes | Unique identifier of the instance evaluated by the experiment |
| `description` | Yes | Commercial/product text used as retrieval query |
| `label` | Yes | Reference HS/NANDINA-compatible code used for evaluation |
| `group_id` | Recommended | Higher-level unit that must remain within one split when instances are dependent |

The actual column names are configured by the user.

Example:

```yaml
dataset:
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: hs_code
```

## Optional logical fields

The framework may consume or derive:

- chapter;
- heading / HS-4;
- HS-6;
- national or regional extension level;
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
- malformed target codes;
- inconsistent hierarchy fields;
- invalid or missing `group_id` when grouped splitting is requested;
- the same `group_id` assigned to multiple splits;
- the same `analysis_id` assigned to multiple splits;
- rows excluded by a configured class/chapter filter without an explicit count;
- unsupported encodings or non-deterministic serialization when a frozen reference preset is requested.

## Group dependence

If multiple rows derive from the same declaration, transaction, document, product family or other higher-level source, that dependency should be represented through `group_id`.

When grouped partitioning is enabled:

```text
one group_id -> exactly one partition
```

This prevents train/evaluation dependence caused by distributing related instances across splits.

## Target-code requirements

The framework should not assume that every experiment uses exactly eight-digit NANDINA codes. The configuration must declare the target coding level or validation rule.

A reference preset may enforce a fixed pattern, for example eight numeric digits and a specific chapter. A custom run may define another compatible HS-based level.

## Query isolation

The retrieval query must not include the reference label or fields derived from it. By default, the following are prohibited as query features unless the protocol explicitly defines a diagnostic experiment:

- true HS/NANDINA code;
- target hierarchy derived from the true label;
- evaluation outcome;
- split membership as semantic content;
- hidden identifiers whose text directly encodes the label.

## Split inputs

Two external-data modes should eventually be supported:

### Pre-split

The user provides historical/dev/eval partitions. The framework validates independence and compatibility but does not repartition them.

### Unsplit

The user provides one dataset plus a split policy. The framework constructs deterministic grouped partitions according to configuration and records the resulting assignments in a manifest.

## Reproducibility metadata for custom data

Every custom run should record:

- dataset path(s);
- SHA-256 of input files;
- row counts;
- column mapping;
- target-code rule;
- grouping-unit definition;
- split policy;
- exact partition counts;
- duplicate/dependence audit results;
- configuration hash;
- framework commit/version.

## Synthetic example

A future stable release should include a small synthetic dataset satisfying this contract so the full interface can be tested without access to administrative data.
