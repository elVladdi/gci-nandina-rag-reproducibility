# Expected results

## Status

This registry is intentionally incomplete while the reference experimental campaign is still being consolidated.

Its purpose is to define numerical and artifact-level acceptance criteria for frozen reproducibility presets.

Custom-data replications do **not** need to match these values. They generate their own independent result manifests.

## Result registry format

Each expected reference result should record:

| Field | Description |
|---|---|
| Result ID | Stable identifier |
| Experiment | Canonical experiment name |
| Input manifest | Frozen input/config manifest |
| Metric/artifact | Metric, table, figure or output file |
| Expected value | Exact value or expected hash |
| Tolerance | Numerical tolerance when exact equality is not appropriate |
| Source commit | Commit from the development repository |
| Research artifact | Report/table/figure identifier when applicable |

## Currently closed reference result

EXP-04 Fase A — Historical BM25 v0.2 has been completed in the development repository. Its final reproducibility package has not yet been imported here because the wider EXP-04 campaign remains open.

Current approved reference metrics on the v0.2 evaluation split:

| Metric | Value |
|---|---:|
| Cases | 1056 |
| Top-1 | 0.509469696969697 |
| Top-3 | 0.6714015151515151 |
| Top-5 | 0.7632575757575758 |
| Top-10 | 0.8910984848484849 |
| Top-50 | 0.9914772727272727 |
| MRR | 0.6297077493524843 |

These values are a provisional cross-reference until a stable reference release freezes its complete acceptance manifest.

## Validation behavior

The future reference runner should compare regenerated outputs with this registry and emit explicit PASS/FAIL results. Exact hashes should be preferred for deterministic artifacts; numerical tolerances should be used only when technically justified.

A custom replication should instead emit its own metrics, configuration hash, input hashes and run manifest without comparing numerical performance to this registry unless the user explicitly requests such a comparison.
