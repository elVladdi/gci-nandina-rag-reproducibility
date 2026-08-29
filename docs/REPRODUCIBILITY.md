# Reproducibility protocol

## Status

This document defines the contract for the future reproducibility package. It does not yet claim end-to-end reproduction because EXP-04 is still in progress in the source repository.

## Source repository

Development repository:

https://github.com/elVladdi/gci-nandina-rag

Every reproducibility release must record:

- source repository URL;
- exact source commit SHA;
- release/tag of this repository;
- dataset and corpus versions;
- configuration hashes;
- execution environment;
- expected output hashes or numerical tolerances.

## Reproducibility levels

The final package should distinguish three levels:

1. **Integrity reproduction** — verify hashes, schema, counts and frozen configuration.
2. **Computational reproduction** — rerun the canonical experiments from frozen inputs.
3. **Result reproduction** — regenerate the thesis metrics, tables and figures and compare them with expected results.

A release should not be described as fully reproducible unless all applicable levels pass from a clean environment.

## Canonical workflow planned

The intended final workflow is:

```text
validate environment
      -> validate inputs
      -> reproduce split/integrity checks when permitted
      -> historical retrieval
      -> normative retrieval baselines
      -> dense/comparator experiments where retained
      -> candidate pools / integration
      -> diagnostic LLM reranking where retained
      -> Top-3 explanation evaluation
      -> integrated error analysis
      -> thesis tables and figures
      -> compare with expected results
```

The exact workflow will be frozen only after EXP-04 is closed.

## Determinism requirements

Where technically possible, final runners must record and enforce:

- explicit random seeds;
- frozen input hashes;
- configuration hashes;
- model identifiers and local model hashes when redistribution permits;
- package versions;
- Python version;
- command line invocation;
- Git commit SHA;
- UTC/local execution timestamp;
- output hashes.

If a component is inherently nondeterministic, the release must document the source of nondeterminism and the numerical or qualitative acceptance criterion.

## Cross-platform serialization

Text artifacts should use LF line endings. Frozen CSV, JSON and text outputs must not change byte representation because of platform-specific checkout behavior.

## Expected final entry point

The target interface is:

```bash
python scripts/reproduce_all.py
```

with optional phase-specific runners. The script should fail fast if any required input hash differs from the release manifest.

## Clean-environment validation

Before a thesis release is published, perform at least one reproduction from a fresh clone in an isolated environment. The validation record should include:

- operating system;
- Python version;
- dependency lock hash;
- source commit;
- runtime duration;
- all PASS/FAIL checks;
- deviations from expected results.

## What does not belong here

This repository should not contain:

- exploratory notebooks unless required to regenerate a final result;
- obsolete experiment versions;
- ad hoc diagnostics;
- unrestricted raw administrative data;
- local model caches;
- thesis drafting files;
- full development history copied from the source repository.

Those remain in the source repository or external archival locations.
