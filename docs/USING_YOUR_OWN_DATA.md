# Using your own data

## Scope

This repository is intended to support replication of the experimental protocol on compatible user-provided data. The objective is to preserve the procedure and evaluation logic, not to force external datasets to resemble the reference dataset in size or distribution.

## Expected workflow

```text
prepare data
  -> map logical fields
  -> validate data contract
  -> validate/create split
  -> audit independence and duplicates
  -> configure retrieval/corpora
  -> run enabled experimental stages
  -> generate metrics and run manifest
```

## 1. Prepare the dataset

A custom dataset should provide at minimum:

- a unique analysis identifier;
- a commercial/product description;
- a reference HS/NANDINA-compatible label;
- preferably a grouping identifier if several rows may originate from the same declaration or dependent source.

See [`DATA_CONTRACT.md`](DATA_CONTRACT.md).

## 2. Create a configuration

Do not rename source columns merely to imitate the reference experiment. Map them in configuration instead.

Example:

```yaml
experiment:
  id: customs_replication_001
  mode: custom

dataset:
  path: data/my_customs.csv
  analysis_unit: line_id
  grouping_unit: declaration_id
  description_column: description
  label_column: hs6
  target_code_level: hs6

split:
  strategy: grouped
  train_ratio: 0.70
  dev_ratio: 0.10
  eval_ratio: 0.20
  seed: 2026
```

A later stable release will provide a validated schema for this file.

## 3. Validate before running

Target interface:

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.yaml
```

The validator should report:

- row count;
- unique analysis units;
- unique groups;
- unique target codes;
- missing descriptions;
- duplicate IDs;
- target-code format;
- group overlap across supplied splits;
- exact/near duplicates across partitions when enabled;
- support of evaluation labels in the historical bank.

Validation warnings and failures must be preserved in the run manifest.

## 4. Split policy

If the user supplies one unsplit dataset, grouped partitioning is recommended whenever related rows share a higher-level source.

The framework should avoid row-wise random splitting when doing so would place dependent observations in different partitions.

If the user supplies pre-existing partitions, they should be audited rather than silently rewritten.

## 5. Historical retrieval

The historical retrieval stage uses the configured description field as query and the historical partition as precedent bank. Target labels are used only for evaluation, not as query content.

## 6. Normative retrieval

A custom run may use a different normative corpus from the reference experiment. The corpus must have explicit provenance, version, code mapping and hash in the run manifest.

## 7. LLM-enabled stages

When enabled, model name/version, parameters, prompt hash, local model identity and candidate constraints should be recorded. A model must not be allowed to access hidden evaluation labels.

## 8. Results

A custom run should generate its own output directory and manifest containing at least:

- experiment ID;
- configuration hash;
- framework version/commit;
- data hashes;
- corpus hashes;
- partition counts;
- retrieval metrics;
- error analysis;
- model configuration where applicable;
- output hashes.

A custom run should not claim to reproduce reference numerical values.

## 9. Comparing with the reference experiment

Comparison is optional. When performed, clearly distinguish:

- differences in dataset composition;
- target-code coverage;
- partition policy;
- normative corpus;
- model/configuration choices;
- resulting performance metrics.

Do not interpret raw metric differences as algorithmic superiority without controlling those factors.

## 10. Data governance

Do not commit confidential or restricted external data to this public repository. Keep such data outside Git or in a local ignored path and record only the metadata/hashes appropriate for the experiment.
