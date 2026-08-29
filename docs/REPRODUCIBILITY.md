# Reproducibility and independent replication

## Scope

This repository publishes a reproducible **protocol and implementation specification**, not the private reference source code.

It supports two scientific goals:

1. **Reference-study verification** — inspect frozen configurations, manifests, hashes, expected metrics and redistributable artifacts from the reference experimental campaign.
2. **Independent replication** — implement the published protocol independently and apply it to a compatible external dataset, tariff hierarchy and normative corpus.

## Reproducibility layers

Reference-study verification should distinguish:

1. **Integrity verification** — hashes, schemas, counts, versions and frozen configuration.
2. **Method verification** — protocol sheets, parameters, invariants, metric definitions and expected information-flow constraints are complete enough for independent implementation.
3. **Result verification** — published expected metrics/artifacts can be traced to frozen manifests and, when permitted, checked against redistributable case-level evidence.

Independent replication should distinguish:

1. data-contract validity;
2. partition-independence validity;
3. component-level conformance to published protocol sheets;
4. conformance to formal input/output specifications;
5. test-vector conformance where available;
6. independent result generation and replication manifest creation.

## Canonical scientific workflow

```text
define taxonomy + target depth + scope
      -> define data contract
      -> define/version normative corpus
      -> validate/create independent partitions
      -> audit dependence, duplicates and support
      -> historical retrieval
      -> normative retrieval
      -> candidate integration / diagnostic reranking where enabled
      -> controlled explanation where enabled
      -> evaluation and error analysis
      -> replication manifest
```

The executable realization of this workflow is deliberately left to the independent implementation. Conformance is evaluated against `protocol/`, `specifications/`, `test_vectors/` and reference manifests, not against private source code.

## Determinism and traceability

Where technically applicable, a replication should record:

- explicit random seeds;
- dataset and corpus identities/hashes;
- configuration hash;
- implementation version/commit;
- library/runtime versions;
- model identifiers and prompt hashes for LLM-enabled stages;
- execution timestamp;
- output hashes;
- declared deviations from the reference protocol.

If a component is nondeterministic, the replicator should document the source and use the tolerance or qualitative acceptance rule defined by the corresponding protocol sheet/test vector.

## Cross-platform artifacts

Published deterministic text artifacts should use stable serialization and LF line endings when frozen hashes are provided.

## What belongs here

This repository may contain:

- protocol sheets and pseudocode;
- formal schemas and configuration examples;
- frozen reference manifests and expected metrics;
- test vectors;
- synthetic examples;
- provenance statements and methodological-validity guidance.

It does not contain:

- private reference source code;
- operational scripts or production pipelines;
- proprietary reusable implementation modules;
- unrestricted administrative data;
- local model caches;
- unrelated development history.

See `docs/REPLICATION_MODEL.md` for the publication boundary.