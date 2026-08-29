# Independent replication publication model

## Decision

This repository publishes a reproducible **experimental protocol and implementation specification**, not the reference source-code implementation.

The publication model intentionally separates scientific disclosure from software distribution:

- the scientific method is documented in sufficient detail for independent implementation;
- experimental decisions, configurations, manifests, hashes and expected results are preserved;
- test vectors and synthetic examples are used to verify independent implementations;
- the reference implementation remains outside this public repository.

## Scientific objective

The repository should support two complementary forms of verification:

1. **Reference-study verification** — inspect the frozen configuration, provenance, expected metrics and artifacts associated with a reference experimental campaign.
2. **Independent replication** — implement the published protocol independently and apply it to another compatible dataset, jurisdiction, tariff hierarchy and normative corpus.

The second form is not expected to reproduce identical numerical results. It tests whether the experimental procedure can be transferred and evaluated independently.

## Required disclosure level

A specification is considered complete only when an independent technical team can implement the component without access to the private reference source code.

Each component sheet should therefore provide:

- objective and experimental role;
- inputs and logical schemas;
- preconditions;
- deterministic or stochastic procedure;
- pseudocode where textual description is insufficient;
- parameters and defaults used by the reference experiment;
- invariants and prohibited information flows;
- outputs and schemas;
- validation rules;
- metrics;
- acceptance criteria;
- edge cases and failure behavior;
- provenance and reproducibility metadata.

## No intentional obscurity

The absence of public source code must not be compensated by an intentionally incomplete methodological description. The protocol should disclose enough information to permit a genuine independent implementation.

## Reference implementation boundary

The following are outside the publication scope of this repository unless a later explicit decision changes that boundary:

- reference source code;
- operational scripts from the private implementation;
- production deployment components;
- reusable proprietary software modules;
- restricted administrative data;
- third-party material without redistribution rights.

## Verification artifacts

Where feasible, the repository should publish:

- configuration examples and frozen reference configurations;
- input/output schemas;
- synthetic datasets;
- test vectors;
- expected invariants;
- case-level reference outputs when legally distributable;
- aggregate expected metrics;
- SHA-256 hashes;
- run manifests;
- data/corpus provenance statements.

## External-data replication

The protocol must remain independent of a particular HS chapter or code depth. A replicator may configure:

- one, multiple or all chapters;
- HS-2, HS-4, HS-6 or compatible regional/national extensions;
- another jurisdiction;
- another historical bank;
- another normative corpus;
- another sample size and distribution.

Changes must be declared in the replication manifest.

## Normative corpus

The normative corpus is part of the experimental configuration, not a fixed repository constant. Its jurisdiction, nomenclature, version, hierarchy coverage and provenance must be documented. Explanations must not claim evidentiary support beyond the corpus coverage actually available.

## Publication-language policy

The main README is maintained in Spanish, English and French. Detailed technical specifications may initially be authored in one language while under active development, but stable replication guidance should progressively provide multilingual navigation or translations where practical.
