# Reproducibility tests

Tests in this repository should validate the scientific package rather than duplicate the full development test suite.

Expected final checks include:

- frozen input hashes;
- schema and cardinality;
- configuration hashes;
- deterministic serialization;
- reproduction of key metrics;
- expected table/figure artifacts;
- consistency with the release manifest.

A clean-clone reproduction should fail loudly when a required input or expected hash is missing or different.
