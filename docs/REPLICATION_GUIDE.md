# Independent replication guide

## Goal

This guide explains how a third party should use the published protocol and specifications to build an independent implementation and evaluate it on its own tariff-classification data.

## Recommended sequence

1. Define the research question, jurisdiction, nomenclature, target depth and experimental scope.
2. Map local data to the logical roles defined in `DATA_CONTRACT.md`.
3. Define and version the normative corpus according to `TAXONOMY_AND_NORMATIVE_CORPUS.md`.
4. Implement the relevant sheets under `protocol/`.
5. Validate input/output contracts under `specifications/`.
6. Validate grouped independence and duplicate controls before final evaluation.
7. Execute the enabled retrieval/ranking/generation stages.
8. Recompute aggregate metrics from case-level outputs.
9. Validate critical components against available `test_vectors/`.
10. Produce a replication manifest containing all deviations from the reference protocol.

## Conformance

An independent implementation may use a different programming language, library stack or infrastructure. Conformance is determined by the published behavioral specifications, invariants, schemas, test vectors and metric definitions—not by similarity to the private reference code.

## External scope

Replicators may change country, tariff hierarchy depth, number of chapters, dataset size, historical bank, normative corpus and enabled experimental stages. Changes must be explicit and must not silently weaken label isolation, partition independence or provenance requirements.

## Reporting

A replication report should distinguish:

- protocol elements preserved exactly;
- implementation choices that are behaviorally equivalent;
- intentionally changed experimental factors;
- unavailable or partially covered normative levels;
- data limitations;
- results and uncertainty/sensitivity analyses;
- deviations that limit direct comparison with the reference study.
