# Reproducibility and replication protocol

## Status

This document defines the contract for the reproducibility repository while the reference experimental campaign is still being consolidated in the development repository.

Source development repository:

https://github.com/elVladdi/gci-nandina-rag

## Two supported goals

This repository must support two distinct scientific uses:

1. **Reference reproduction** — rerun a frozen experimental preset with the same inputs, configuration and expected outputs.
2. **External replication** — execute the same protocol on a compatible user-provided dataset and produce a new manifest and result set.

External replication does not require numerical agreement with the reference experiment. What must remain comparable is the protocol, data contract, evaluation logic and reporting structure.

## Reproducibility levels

A reference release should distinguish:

1. **Integrity reproduction** — verify hashes, schema, counts and frozen configuration.
2. **Computational reproduction** — rerun canonical experiments from frozen inputs.
3. **Result reproduction** — regenerate registered metrics and artifacts and compare them with expected results.

A release should not be described as fully reproducible unless all applicable levels pass from a clean environment.

## Replication levels for user-provided data

A custom run should distinguish:

1. **Data-contract validation** — required logical fields can be mapped and satisfy integrity constraints.
2. **Partition validation** — no forbidden overlap exists across splits, especially at configured grouping-unit level.
3. **Protocol execution** — canonical retrieval/evaluation stages run without source-code modification.
4. **Independent result generation** — metrics, manifests and error analyses are produced for the external dataset.

## Canonical workflow

```text
validate environment
      -> validate configuration
      -> validate data contract
      -> validate/create grouped split
      -> audit duplicates and support
      -> historical retrieval
      -> normative retrieval baselines
      -> dense/comparator experiments where enabled
      -> candidate pools / integration
      -> diagnostic LLM reranking where enabled
      -> controlled Top-k explanation where enabled
      -> integrated error analysis
      -> generate run manifest and metrics
```

A frozen reference preset may additionally compare generated outputs with exact expected values or hashes.

## Configuration principle

Dataset-specific decisions must be supplied through configuration rather than hard-coded globally. At minimum, the configuration layer should be able to identify:

- analysis unit;
- grouping unit;
- commercial-description/query field;
- target code field;
- optional class/chapter filter;
- split policy;
- retrieval strategy settings;
- normative corpus configuration;
- enabled/disabled experimental stages.

## Determinism requirements

Where technically possible, runners must record and enforce:

- explicit random seeds;
- frozen input hashes for reference presets;
- configuration hashes;
- model identifiers and local model hashes when redistribution permits;
- package versions;
- Python version;
- command line invocation;
- Git commit SHA;
- execution timestamp;
- output hashes.

If a component is inherently nondeterministic, document the source of nondeterminism and the numerical or qualitative acceptance criterion.

## Cross-platform serialization

Text artifacts should use LF line endings. Frozen CSV, JSON and text outputs must not change byte representation because of platform-specific checkout behavior.

## Target entry points

Reference reproduction:

```bash
python scripts/reproduce_all.py --config configs/presets/reference_class87_v0.2.yaml
```

External replication:

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.yaml
python scripts/run_experiment.py --config configs/examples/custom_dataset.yaml
```

These interfaces are targets until the corresponding canonical runners are frozen.

## Clean-environment validation

Before a stable reference release is published, perform at least one reproduction from a fresh clone in an isolated environment and record:

- operating system;
- Python version;
- dependency lock hash;
- source commit;
- runtime duration;
- PASS/FAIL checks;
- deviations from expected results.

A custom-data example should also be validated from a clean environment using a small synthetic dataset.

## What does not belong here

This repository should not contain:

- exploratory notebooks unless required by a canonical release;
- obsolete experiment versions;
- ad hoc diagnostics;
- unrestricted raw administrative data;
- local model caches;
- manuscript/drafting files;
- full development history copied from the source repository.

Those remain in the development repository or external archival locations.
