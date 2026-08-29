# Tariff taxonomy and normative corpus

## Purpose

This document defines the relationship between the tariff hierarchy being predicted and the documentary corpus used to support normative retrieval and controlled explanation.

The framework is intentionally agnostic to a specific chapter, jurisdiction, regional nomenclature or fixed number of digits.

## 1. Classification hierarchy

A run must explicitly declare:

- `family`: usually HS-rooted;
- `jurisdiction`: international, regional or national context;
- `nomenclature`: the concrete tariff nomenclature being evaluated;
- `target_digits`: depth of the primary prediction/evaluation target;
- `hierarchy_digits`: levels available for validation and hierarchical analysis.

Example:

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]
```

The framework must not assume that eight-digit NANDINA codes are the deepest or universal target. NANDINA is one possible regional implementation. National tariff lines may extend the hierarchy further.

## 2. Scope is independent from taxonomy depth

A study may cover:

- a single chapter;
- several selected chapters;
- all chapters present in the dataset.

Example:

```yaml
scope:
  chapters: null
```

`null` means no chapter restriction. Chapter selection is an experimental sampling/scope decision and must not alter the generic classification contract.

## 3. Normative corpus layers

The normative corpus may combine several documentary layers, depending on jurisdiction and target depth:

1. HS nomenclature and descriptions;
2. General Rules for the Interpretation;
3. section and chapter notes;
4. HS explanatory or other permitted reference material where available;
5. regional nomenclature and notes;
6. national tariff nomenclature;
7. national complementary/supplementary notes or other officially defined sources.

Every corpus layer must have explicit provenance, version and redistribution status.

## 4. Corpus coverage contract

The corpus configuration must declare the hierarchy levels it can support:

```yaml
normative:
  corpus_path: data/normative/my_jurisdiction/
  jurisdiction: CL
  nomenclature: national_tariff
  supported_digits: [2, 4, 6, 8, 10]
```

`classification.target_digits` and `normative.supported_digits` are related but not necessarily equal.

### Full normative coverage

```text
target_digits = 10
supported_digits = [2, 4, 6, 8, 10]
```

Normative evidence may support distinctions down to the target level, subject to document retrieval quality.

### Partial normative coverage

```text
target_digits = 10
supported_digits = [2, 4, 6, 8]
```

Historical retrieval may still rank 10-digit codes, but the normative corpus cannot independently ground the final 10-digit distinction. The run must record that limitation.

## 5. Required validation behavior

Before normative retrieval, integration or explanation, validate:

- corpus jurisdiction matches or is explicitly intended for the run;
- corpus nomenclature/version is declared;
- supported hierarchy levels are known;
- code mappings are valid for the selected taxonomy;
- target codes can be linked to the available normative hierarchy;
- any target-depth coverage gap is reported.

A configuration such as the following must never pass silently:

```text
classification: national 10-digit tariff
normative corpus: unrelated jurisdiction
```

## 6. Explanation boundary

Controlled generation must never imply more documentary precision than the retrieved corpus supports.

If evidence supports an HS-6 or 8-digit parent but not the national 10-digit child, the explanation must distinguish:

- what is supported by normative evidence;
- what is supported only by historical precedents or candidate structure;
- what remains unsupported or requires expert validation.

## 7. Versioning

Tariff nomenclatures and normative documents change over time. Each run must therefore record:

- nomenclature version/year;
- corpus build/version;
- source URLs or identifiers where applicable;
- acquisition/build date;
- corpus SHA-256 or manifest hash;
- target taxonomy version;
- any cross-version mapping used.

## 8. External replication

A customs administration replicating the protocol should normally provide its own national or regional normative corpus when evaluating classifications deeper than internationally harmonized HS levels.

The framework reproduces the experimental method; it does not assume that the normative sources of one jurisdiction are valid for another.
