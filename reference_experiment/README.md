# Reference experiment

This directory will preserve the **frozen experimental evidence** needed to document and verify the reference study without publishing its private implementation.

Planned structure:

- `configuration/` — frozen experimental settings;
- `manifests/` — source/data/corpus/output identities and hashes;
- `expected_metrics/` — machine-readable expected metrics;
- `expected_results/` — redistributable case-level or aggregate reference artifacts.

Reference-specific choices (for example a restricted chapter or a particular code depth) belong here and must not become universal protocol assumptions.