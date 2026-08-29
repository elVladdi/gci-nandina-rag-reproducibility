# Protocol specification index

This directory contains the component-level implementation sheets required for independent replication. It documents **what an implementation must do**, not the private reference source code.

## Sheet contract

Every protocol sheet must contain:

1. identifier and title;
2. objective and experimental role;
3. inputs;
4. preconditions;
5. procedure and pseudocode where needed;
6. configurable parameters;
7. invariants and prohibited information flows;
8. outputs;
9. validations;
10. metrics;
11. acceptance criteria;
12. edge cases and failure behavior;
13. reproducibility metadata;
14. linked formal specifications;
15. linked test vectors.

## Planned catalogue

- `00_overview/` — end-to-end experimental flow and component dependencies.
- `01_data/` — input validation, normalization and tariff-hierarchy validation.
- `02_splitting/` — group-safe partitioning, support constraints and duplicate audits.
- `03_retrieval/` — historical, normative and optional dense retrieval.
- `04_ranking/` — candidate integration, fixed candidate selection and diagnostic reranking.
- `05_rag/` — context construction, controlled explanation and auditable output.
- `06_evaluation/` — Top-k, MRR, coverage, hierarchy and error analyses.
- `07_audit/` — integrity, provenance and replication manifests.

The catalogue will be populated only from experimentally closed components. A missing sheet means **not yet specified**, not an implicit default.