# Expected results

## Status

This file is intentionally incomplete until EXP-04 and the final thesis analysis are closed.

Its final purpose is to define the numerical and artifact-level acceptance criteria for a reproducibility release.

## Result registry format

Each expected result should record:

| Field | Description |
|---|---|
| Result ID | Stable identifier |
| Experiment | Canonical experiment name |
| Input manifest | Frozen input/config manifest |
| Metric/artifact | Metric, table, figure or output file |
| Expected value | Exact value or expected hash |
| Tolerance | Numerical tolerance when exact equality is not appropriate |
| Source commit | Commit from the development repository |
| Thesis location | Table/figure/section using the result |

## Currently closed source result

At scaffold creation time, EXP-04 Fase A — Historical BM25 v0.2 has been completed in the development repository, but its final reproducibility package has not yet been imported here because EXP-04 as a whole remains open.

The source repository currently reports the following approved Fase A metrics on the v0.2 evaluation split:

| Metric | Value |
|---|---:|
| Cases | 1056 |
| Top-1 | 0.509469696969697 |
| Top-3 | 0.6714015151515151 |
| Top-5 | 0.7632575757575758 |
| Top-10 | 0.8910984848484849 |
| Top-50 | 0.9914772727272727 |
| MRR | 0.6297077493524843 |

These values are documented here only as a provisional cross-reference. They do not yet constitute a release acceptance contract for this repository.

## Final validation behavior

The future reproduction runner should compare regenerated outputs with this registry and emit explicit PASS/FAIL results. Exact hashes should be preferred for deterministic artifacts; numerical tolerances should be used only when technically justified.
