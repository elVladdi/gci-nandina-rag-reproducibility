# Expected reference results

## Purpose

This registry records numerical and artifact-level evidence from frozen reference experiments. It is used for traceability and independent verification of the published protocol; it is **not** an executable acceptance suite for private reference code.

External replications do not need to match these values. They should generate their own metrics and replication manifests.

## Registry format

Each expected reference result should record:

| Field | Description |
|---|---|
| Result ID | Stable identifier |
| Experiment | Reference experiment identifier |
| Input manifest | Frozen data/corpus/configuration manifest |
| Protocol sheet(s) | Published component specifications governing the result |
| Metric/artifact | Metric, table, figure or case-level artifact |
| Expected value | Exact value or expected hash where appropriate |
| Tolerance | Numerical tolerance when exact equality is not justified |
| Source implementation record | Private/reference implementation commit or version identifier when disclosure is permitted |
| Research artifact | Report/table/figure identifier when applicable |

## Current closed reference evidence

The development project has completed a historical BM25 reference evaluation on a frozen v0.2 evaluation split. The currently approved aggregate values are:

| Metric | Value |
|---|---:|
| Cases | 1056 |
| Top-1 | 0.509469696969697 |
| Top-3 | 0.6714015151515151 |
| Top-5 | 0.7632575757575758 |
| Top-10 | 0.8910984848484849 |
| Top-50 | 0.9914772727272727 |
| MRR | 0.6297077493524843 |

These values remain provisional registry entries until the corresponding protocol sheet, manifest, redistributable evidence and test vectors are frozen in this repository.

## Independent-implementation verification

A third party may use published test vectors and protocol invariants to validate component conformance. Matching the full reference metric table is neither required nor expected when the third party uses different data, taxonomy, corpus, scope or implementation choices allowed by the protocol.

## External replication reporting

A replication should report its own:

- dataset/corpus/configuration identities;
- protocol-sheet versions implemented;
- declared deviations;
- case-level outputs where permissible;
- aggregate metrics;
- sensitivity/error analyses;
- implementation/runtime metadata;
- replication manifest.
