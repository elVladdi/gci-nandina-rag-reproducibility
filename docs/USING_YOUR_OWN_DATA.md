# Using your own data

## Scope

This repository supports replication of the experimental protocol on compatible user-provided customs data. External datasets do not need to match the reference study in country, chapter coverage, sample size, code distribution, nomenclature extension or target-code depth.

A replication may use one chapter, several chapters, or the entire tariff universe available in the data.

## Expected workflow

```text
prepare customs data
  -> define tariff hierarchy and target level
  -> map logical fields
  -> configure normative corpus
  -> validate data and taxonomy contracts
  -> validate/create grouped split
  -> audit independence and duplicates
  -> run historical retrieval
  -> run normative retrieval
  -> run optional integration/LLM stages
  -> generate metrics, errors and manifest
```

## 1. Prepare the dataset

At minimum provide:

- a unique analysis identifier;
- a commercial/product description;
- a reference tariff label at the chosen target level;
- preferably a grouping identifier when several rows originate from a common declaration or dependent source.

See [`DATA_CONTRACT.md`](DATA_CONTRACT.md).

## 2. Define the classification system

Do not assume that the target must be NANDINA-8. Declare the jurisdiction, nomenclature and target depth explicitly.

Example for a national 10-digit tariff:

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]
```

For an HS-6 study:

```yaml
classification:
  family: HS
  jurisdiction: international
  nomenclature: HS
  target_digits: 6
  hierarchy_digits: [2, 4, 6]
```

## 3. Define experimental scope

All chapters:

```yaml
scope:
  chapters: null
```

A subset:

```yaml
scope:
  chapters: [84, 85, 87]
```

Filtering by chapter is a study-design choice. The framework itself is chapter-agnostic.

## 4. Configure your data mapping

```yaml
experiment:
  id: customs_replication_001
  mode: custom

dataset:
  path: data/my_customs.csv
  analysis_unit: line_id
  grouping_unit: declaration_id
  description_column: description
  label_column: national_tariff_code
```

Do not rename source columns merely to imitate a reference dataset.

## 5. Configure the normative corpus

The explanation/evidence pipeline depends on the jurisdiction and target nomenclature. A user working at a national 10-digit level should provide normative material that supports the relevant national extension whenever available.

```yaml
normative:
  enabled: true
  corpus_path: data/normative/my_jurisdiction/
  jurisdiction: CL
  nomenclature: national_tariff
  supported_digits: [2, 4, 6, 8, 10]
```

Possible corpus layers include international HS material, section/chapter notes, interpretative rules, regional nomenclature and national tariff or complementary notes.

If normative coverage is shallower than the target code, the run must disclose that mismatch. The generation stage must not present unsupported national-level detail as if it were grounded in the corpus.

See [`TAXONOMY_AND_NORMATIVE_CORPUS.md`](TAXONOMY_AND_NORMATIVE_CORPUS.md).

## 6. Validate before running

Target interface:

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.example.yaml
```

The validator should report:

- row count;
- unique analysis units and groups;
- unique target codes;
- label-format compatibility with target depth;
- hierarchy consistency;
- chapter scope actually retained;
- missing descriptions and duplicate IDs;
- group overlap across supplied splits;
- exact/near duplicates across partitions when enabled;
- support of evaluation labels in the historical bank;
- normative corpus identity and supported hierarchy levels;
- any gap between classification depth and normative-evidence depth.

## 7. Split policy

If the dataset is unsplit, grouped partitioning is recommended whenever related rows share a higher-level source. Row-wise random splitting should not be used when it would place dependent observations in different partitions.

If pre-existing partitions are supplied, audit them rather than silently rewriting them.

## 8. Historical retrieval

Historical retrieval uses the configured description as query and the historical partition as precedent bank. Ground-truth labels are evaluation targets only and must not become retrieval features.

The ranked unit may be the configured target code while hierarchical metrics can additionally evaluate errors at coarser levels.

## 9. Normative retrieval

Normative retrieval is specific to the configured corpus. Its version, jurisdiction, nomenclature, supported levels, code mapping and SHA-256 must be recorded.

Comparisons between historical and normative strategies must use the same evaluation instances when direct metric comparison is claimed.

## 10. LLM-enabled stages

When enabled, record model name/version, parameters, prompt hash, local model identity and candidate constraints. The model must not receive hidden evaluation labels.

The explanation must distinguish evidence available at each hierarchy level and explicitly acknowledge missing normative coverage.

## 11. Results and manifest

A custom run should record at least:

- experiment ID;
- framework version/commit;
- configuration hash;
- data and corpus hashes;
- classification system, jurisdiction and target depth;
- chapter scope;
- partition counts;
- historical and normative coverage;
- retrieval metrics and hierarchy-aware error analysis;
- LLM configuration where applicable;
- output hashes and warnings.

A custom run should not claim to reproduce reference numerical values.

## 12. Data governance

Do not commit confidential or restricted customs data to this public repository. Keep such data in a local ignored path or controlled storage and record only metadata/hashes appropriate for reproducibility.
