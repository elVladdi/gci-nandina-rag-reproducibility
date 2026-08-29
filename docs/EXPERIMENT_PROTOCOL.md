# Experimental protocol

## Purpose

This document defines the protocol-level structure that should remain stable across reference reproduction and external replication. Dataset-specific values belong in configuration presets, not in this document.

## 1. Input definition

Each run must define:

- analysis unit;
- grouping unit when dependent observations exist;
- description/query field;
- target-code field and coding level;
- historical/dev/evaluation partitions or a deterministic split policy;
- normative corpus when normative stages are enabled;
- enabled experimental stages.

## 2. Data validation

Before retrieval, validate:

- required fields;
- unique analysis IDs;
- target-code format/hierarchy;
- non-empty descriptions;
- grouping-unit integrity;
- partition exclusivity;
- dataset hashes and configuration hash.

## 3. Independence audit

For historical/evaluation comparisons, inspect at minimum:

- shared grouping units across partitions;
- shared analysis IDs;
- exact description duplicates;
- configured near-duplicate diagnostics;
- target-code support in the historical bank.

The audit should be reported, not silently repaired after evaluation results are known.

## 4. Historical retrieval

The historical retrieval stage ranks precedents/codes using the commercial description and the historical bank.

Canonical requirements:

- no access to the true evaluation label as query input;
- no use of grouping identifiers as semantic query features;
- preserve the configured candidate de-duplication rule;
- record ranking depth and retrieval parameters;
- report Top-k and MRR where defined.

## 5. Normative retrieval

Normative retrieval provides documentary evidence from a versioned normative corpus.

Strategies may include flat, hierarchical or dense retrieval when enabled. Each strategy must use the same evaluation case set for direct comparative claims.

## 6. Candidate integration

When historical and normative retrieval are combined:

- provenance of every candidate/evidence item must be retained;
- integration rules must be configured and recorded;
- the reference label must remain hidden from ranking/generation stages.

## 7. Diagnostic reranking

LLM reranking, when enabled, is treated as a diagnostic/secondary stage unless a specific research preset states otherwise.

The reranker should:

- operate only on an allowed candidate pool;
- record before/after ranking;
- preserve prompt/model metadata;
- report win/tie/loss and ranking deltas;
- never access the true label.

## 8. Controlled explanation

A controlled explanation stage may receive a fixed Top-k candidate set plus retrieved evidence and output an auditable comparison/justification.

The candidate-set size must be specified by configuration. A reference preset may freeze `Top-3`; custom replications may retain that preset for comparability or define another experimental setting explicitly.

## 9. Evaluation

At minimum, retrieval experiments should report metrics appropriate to their strategy, such as:

- Top-1;
- Top-3;
- Top-5;
- Top-10;
- deeper recall/Top-k where configured;
- MRR;
- corpus coverage;
- hierarchical error distance;
- subgroup/sensitivity analyses defined before interpretation.

## 10. Error analysis

The run should preserve case-level information sufficient to distinguish:

- ranking failure;
- absence of historical support;
- absence of normative coverage;
- duplicate/near-duplicate effects;
- hierarchy-near errors;
- candidate-pool omission;
- reranking/generation effects where applicable.

## 11. Run manifest

Every canonical run should record:

- experiment ID;
- framework commit/version;
- configuration path/hash;
- input paths/hashes;
- corpus paths/hashes;
- split counts;
- parameters/seeds;
- model/prompt identifiers where applicable;
- command;
- environment versions;
- timestamp;
- output files/hashes;
- aggregate metrics;
- warnings/deviations.

## 12. Interpretation boundary

The protocol evaluates an auditable recommendation workflow under controlled experimental conditions. Its outputs are not official customs classifications and do not replace expert or legal review.
