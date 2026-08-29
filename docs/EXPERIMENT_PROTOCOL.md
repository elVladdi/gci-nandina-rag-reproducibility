# Experimental protocol

## Purpose

This document defines the protocol-level structure that should remain stable across reference reproduction and external replication. Jurisdiction, chapter scope, nomenclature and target-code depth belong in configuration, not in framework assumptions.

## 1. Input definition

Each run must define:

- analysis unit;
- grouping unit when dependent observations exist;
- description/query field;
- classification family and jurisdiction;
- target nomenclature and target-code depth;
- hierarchy levels available for evaluation;
- chapter scope (`all`, one chapter or multiple chapters);
- historical/dev/evaluation partitions or a deterministic split policy;
- normative corpus and its supported hierarchy levels when normative stages are enabled;
- enabled experimental stages.

## 2. Classification hierarchy

The protocol is designed for hierarchical tariff systems rooted in HS but is not limited to one fixed depth.

A run may target, for example:

- HS-2;
- HS-4;
- HS-6;
- an 8-digit regional or national extension;
- a 10-digit national extension;
- another explicitly configured compatible hierarchy.

The target level determines the primary ground-truth label. Coarser configured levels may be used for hierarchical error analysis.

## 3. Data validation

Before retrieval, validate:

- required fields;
- unique analysis IDs;
- target-code format and length;
- hierarchy-prefix consistency;
- non-empty descriptions;
- grouping-unit integrity;
- partition exclusivity;
- chapter-scope filtering counts;
- dataset hashes and configuration hash.

## 4. Independence audit

Inspect at minimum:

- shared grouping units across partitions;
- shared analysis IDs;
- exact description duplicates;
- configured near-duplicate diagnostics;
- target-code support in the historical bank.

The audit must be reported rather than silently repaired after evaluation results are known.

## 5. Historical retrieval

Historical retrieval ranks precedents/codes using commercial descriptions and the historical bank.

Canonical requirements:

- no access to true evaluation labels as query input;
- no use of grouping identifiers as semantic query features;
- ranking performed at the configured target-code level;
- preserve configured candidate de-duplication rules;
- record ranking depth and parameters;
- report Top-k and MRR where defined;
- report hierarchical error distance using configured coarser levels where applicable.

## 6. Normative retrieval

Normative retrieval provides documentary evidence from a versioned corpus whose scope must be explicitly associated with:

- jurisdiction;
- nomenclature;
- source/version;
- hierarchy levels supported;
- code mappings where applicable.

Strategies may include flat, hierarchical or dense retrieval. Each directly compared strategy must use the same evaluation case set.

A normative corpus is not assumed to be universal. Changing from HS-6 to a regional or national extension may require changing or augmenting the corpus.

## 7. Normative-depth compatibility

Before normative retrieval or explanation, compare the configured target depth with corpus coverage.

Example:

```text
classification.target_digits = 10
normative.supported_digits = [2, 4, 6, 8]
```

This run may still evaluate 10-digit historical retrieval, but normative evidence reaches only 8 digits. The limitation must be recorded and the explanation stage must not claim documentary grounding for unsupported 10-digit distinctions.

## 8. Candidate integration

When historical and normative retrieval are combined:

- provenance of every candidate/evidence item must be retained;
- integration rules must be configured and recorded;
- evidence hierarchy level must be retained;
- the reference label must remain hidden from ranking/generation stages.

## 9. Diagnostic reranking

LLM reranking, when enabled, is a diagnostic/secondary stage unless a research preset explicitly defines otherwise.

It should:

- operate only on an allowed candidate pool;
- record before/after ranking;
- preserve prompt/model metadata;
- report win/tie/loss and ranking deltas;
- never access the true label.

## 10. Controlled explanation

The explanation stage may receive a fixed Top-k candidate set plus historical and normative evidence.

It must:

- preserve evidence provenance;
- distinguish evidence by hierarchy level;
- avoid implying support below the deepest normative level actually covered;
- record candidate-set size, model, prompt and parameters;
- keep the true evaluation label hidden.

## 11. Evaluation

Metrics may include:

- Top-1, Top-3, Top-5, Top-10 and deeper Top-k;
- MRR;
- historical support coverage;
- normative corpus coverage;
- hierarchical error distance;
- per-level correctness (for example HS-6 correct while national extension incorrect);
- subgroup/sensitivity analyses defined before interpretation.

When the experimental scope contains all or multiple chapters, cross-chapter error analysis should be retained. A single-chapter study naturally constrains that dimension and must not be treated as a framework limitation.

## 12. Error analysis

Preserve case-level information sufficient to distinguish:

- ranking failure;
- absence of historical support;
- absence or insufficient depth of normative coverage;
- duplicate/near-duplicate effects;
- hierarchy-near errors;
- cross-chapter errors when possible;
- candidate-pool omission;
- reranking/generation effects where applicable.

## 13. Run manifest

Every canonical run should record:

- experiment ID;
- framework commit/version;
- configuration path/hash;
- classification family, jurisdiction and nomenclature;
- target digits and hierarchy levels;
- chapter scope;
- input paths/hashes;
- normative corpus paths/hashes and supported depths;
- split counts;
- parameters/seeds;
- model/prompt identifiers where applicable;
- command;
- environment versions;
- timestamp;
- output files/hashes;
- aggregate metrics;
- warnings and coverage gaps.

## 14. Interpretation boundary

The protocol evaluates an auditable tariff-classification recommendation workflow under controlled experimental conditions. Outputs are not official customs classifications and do not replace expert or legal review.
