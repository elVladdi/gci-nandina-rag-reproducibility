# Tests

Tests in this repository should validate the scientific reproducibility package without duplicating the full development test suite.

Reference-release checks should include:

- frozen input hashes;
- schema and cardinality;
- configuration hashes;
- deterministic serialization;
- reproduction of key metrics/artifacts;
- consistency with the release manifest.

Reusable-framework checks should additionally include:

- custom logical-column mapping;
- dataset-contract failures and warnings;
- grouped split independence;
- duplicate/dependence audits;
- target-code validation at configurable hierarchy levels;
- generation of a complete custom-run manifest;
- end-to-end execution against a small synthetic dataset.

A clean-clone reference reproduction should fail loudly when a required frozen input or expected hash differs. A custom run should fail clearly when its data contract or configured partition rules are violated.
