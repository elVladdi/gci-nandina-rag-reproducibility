# gci-nandina-rag-reproducibility

**Framework experimental reproducible para recomendación auditable de clasificación arancelaria jerárquica**  
**Reproducible experimental framework for auditable hierarchical tariff-classification recommendation**  
**Cadre expérimental reproductible pour la recommandation auditable de classification tarifaire hiérarchique**

[Español](#español) · [English](#english) · [Français](#français)

Repositorio de desarrollo relacionado / Related development repository / Dépôt de développement associé: [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag)

---

# Español

## ¿Qué es este repositorio?

Este repositorio proporciona una implementación reproducible y parametrizable de un **protocolo experimental para recomendación auditable de clasificación arancelaria jerárquica**. El protocolo combina recuperación histórica, recuperación de evidencia normativa y, cuando se habilita, generación controlada con un LLM local.

```text
Descripción comercial
        │
        ▼
Recuperación histórica ─────► precedentes y candidatos
        │
        ├────────────────────► evidencia normativa compatible
        │
        ▼
Integración / contexto
        │
        ▼
LLM local opcional
        │
        ▼
Salida auditable + métricas + trazabilidad
```

El framework **no está limitado a un capítulo, clase, país, número fijo de dígitos ni a NANDINA**. Puede utilizarse con un único capítulo, varios capítulos o todo el universo arancelario disponible, y con distintos niveles de la jerarquía HS o extensiones regionales/nacionales, siempre que los datos y el corpus normativo sean compatibles con el nivel objetivo.

No emite una clasificación aduanera jurídicamente vinculante y no sustituye la revisión experta.

## Modos de uso

| Modo | Datos | Objetivo |
|---|---|---|
| `reference` | Dataset, configuración y artefactos congelados | Reproducir un estudio experimental de referencia |
| `custom` | Dataset y corpus proporcionados por el usuario | Replicar el protocolo con datos independientes |
| `synthetic` | Dataset demostrativo | Probar instalación y flujo sin datos sensibles |

> Una ejecución con datos propios replica el **protocolo experimental**; no se espera que reproduzca los mismos valores numéricos del estudio de referencia.

## ¿Qué sistemas y niveles de clasificación admite?

La jerarquía objetivo es configurable. Ejemplos:

| Nivel | Ejemplo conceptual |
|---|---|
| HS-2 | Capítulo |
| HS-4 | Partida |
| HS-6 | Subpartida del Sistema Armonizado |
| 8 dígitos | Extensión regional o nacional, por ejemplo NANDINA cuando corresponda |
| 10 dígitos | Subpartida nacional u otra extensión jurisdiccional |

La configuración debe declarar la nomenclatura, la jurisdicción, los niveles jerárquicos disponibles y el nivel objetivo. Por ejemplo:

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null   # null = todos los capítulos presentes en el dataset
```

También puede restringirse el experimento:

```yaml
scope:
  chapters: [84, 85]
```

La selección de uno o varios capítulos es una **decisión experimental**, no una limitación del framework.

## El corpus normativo también es configurable

La evidencia normativa debe corresponder a la nomenclatura y jurisdicción que se están evaluando. Un corpus adecuado puede incluir, según el experimento:

- nomenclatura HS internacional;
- notas de sección y capítulo;
- Reglas Generales para la Interpretación;
- extensiones regionales;
- arancel o nomenclatura nacional;
- notas complementarias nacionales;
- otros documentos normativos definidos y versionados por la investigación.

Ejemplo:

```yaml
normative:
  enabled: true
  corpus_path: data/normative/chile_2026/
  jurisdiction: CL
  nomenclature: national_tariff
  supported_digits: [2, 4, 6, 8, 10]
```

El framework debe detectar incompatibilidades entre el nivel objetivo y la cobertura normativa. Por ejemplo, si la clasificación objetivo es de 10 dígitos pero el corpus solo aporta evidencia hasta 8, esa limitación debe quedar explícita en los resultados y nunca ocultarse.

## ¿Puedo usar mis propios datos?

**Sí.** Los nombres de columnas, tamaño de muestra, país, capítulos presentes y distribución de códigos son configurables.

| Rol lógico | Obligatorio | Significado |
|---|---:|---|
| `analysis_id` | Sí | Identificador único de la instancia evaluada |
| `description` | Sí | Descripción comercial usada como consulta |
| `label` | Sí | Código arancelario de referencia en el nivel objetivo |
| `group_id` | Recomendado | Unidad superior que no debe cruzar particiones cuando existe dependencia |

Ejemplo:

```csv
item_id,declaration_id,product_description,tariff_code
CL-0001,D-001,"MOTOR ELECTRICO INDUSTRIAL",8501520010
CL-0002,D-001,"TABLERO DE CONTROL ELECTRICO",8537109090
CL-0003,D-002,"BOMBA CENTRIFUGA",8413700090
```

```yaml
experiment:
  id: customs_replication
  mode: custom

dataset:
  path: data/customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: tariff_code

classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null
```

## Regla crítica: independencia entre particiones

Si varias filas pertenecen a una misma declaración, transacción, embarque, factura u otra unidad dependiente, todos los registros de ese grupo deben permanecer en una sola partición.

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

El framework debe auditar además IDs compartidos, duplicados exactos, near-duplicates y soporte histórico del código objetivo.

## Flujo de una replicación externa

```text
proveer datos + nomenclatura + corpus normativo
        ↓
mapear campos lógicos
        ↓
validar jerarquía y cobertura normativa
        ↓
crear o validar particiones independientes
        ↓
auditar dependencia y duplicados
        ↓
recuperación histórica
        ↓
recuperación normativa
        ↓
integración / LLM opcional
        ↓
evaluación, errores y manifest reproducible
```

## Quick start objetivo

```bash
git clone https://github.com/elVladdi/gci-nandina-rag-reproducibility.git
cd gci-nandina-rag-reproducibility
python -m venv .venv
pip install -r requirements.txt
```

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.example.yaml
python scripts/run_experiment.py --config configs/examples/custom_dataset.example.yaml
```

Para una reproducción exacta de un estudio de referencia se utilizará el preset versionado correspondiente:

```bash
python scripts/reproduce_all.py --config configs/presets/<reference-preset>.yaml
```

> Estos comandos representan la interfaz objetivo y se implementarán progresivamente a medida que se congelen los componentes experimentales.

## ¿Cuándo una réplica es metodológicamente válida?

Como mínimo debe:

- definir explícitamente la nomenclatura y el nivel objetivo;
- utilizar etiquetas compatibles con ese nivel;
- registrar la versión y cobertura del corpus normativo;
- mantener independientes los grupos relacionados entre particiones;
- impedir que la etiqueta verdadera entre en la consulta o generación;
- comparar estrategias sobre las mismas instancias de evaluación;
- evitar ajuste de parámetros sobre el conjunto final de evaluación;
- preservar configuración, hashes y procedencia;
- permitir recalcular métricas desde resultados por caso;
- declarar cualquier limitación de cobertura histórica o normativa.

## ¿Qué puede variar?

País, capítulos, cantidad de capítulos, nivel objetivo, extensión regional/nacional, dataset, nombres de columnas, banco histórico, corpus normativo, tamaño de muestra, modelo local y proporciones de partición pueden variar. Los controles de independencia, aislamiento de etiquetas, trazabilidad y comparabilidad experimental deben preservarse.

## Documentación

- [`docs/USING_YOUR_OWN_DATA.md`](docs/USING_YOUR_OWN_DATA.md)
- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md)
- [`docs/TAXONOMY_AND_NORMATIVE_CORPUS.md`](docs/TAXONOMY_AND_NORMATIVE_CORPUS.md)
- [`docs/EXPERIMENT_PROTOCOL.md`](docs/EXPERIMENT_PROTOCOL.md)
- [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md)
- [`docs/DATA_PROVENANCE.md`](docs/DATA_PROVENANCE.md)
- [`docs/EXPECTED_RESULTS.md`](docs/EXPECTED_RESULTS.md)

---

# English

## What is this repository?

This repository provides a reproducible and configurable implementation of an **experimental protocol for auditable hierarchical tariff-classification recommendation**. It combines historical retrieval, normative-evidence retrieval, and optional controlled generation with a local LLM.

The framework is **not restricted to a specific chapter, class, country, fixed code length, or NANDINA**. It can operate on one chapter, multiple chapters, or the complete tariff universe represented in the supplied data. The target can be HS-2, HS-4, HS-6, an 8-digit regional/national extension, a 10-digit national tariff line, or another compatible hierarchical extension.

It does not issue legally binding customs classifications and does not replace expert review.

## Usage modes

| Mode | Data | Goal |
|---|---|---|
| `reference` | Frozen data/configuration | Reproduce a reference experimental study |
| `custom` | User-provided data and corpus | Replicate the protocol on independent data |
| `synthetic` | Demonstration data | Test installation and workflow |

## Configurable tariff hierarchy

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null   # all chapters represented in the dataset
```

A chapter restriction is optional and experimental, not a framework constraint.

## Configurable normative corpus

Normative evidence must be versioned and compatible with the selected jurisdiction, nomenclature, and target level. It may combine international HS material, section/chapter notes, interpretative rules, regional extensions, national tariff nomenclature and national complementary notes.

```yaml
normative:
  enabled: true
  corpus_path: data/normative/chile_2026/
  jurisdiction: CL
  nomenclature: national_tariff
  supported_digits: [2, 4, 6, 8, 10]
```

If the target is 10 digits but the normative corpus only supports 8, the framework must report that evidence-coverage limitation explicitly.

## Using your own data

Required logical roles are `analysis_id`, `description`, `label`, and preferably `group_id` when related observations share a parent entity. Source column names are configurable.

```yaml
experiment:
  id: customs_replication
  mode: custom

dataset:
  path: data/customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: tariff_code

classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null
```

## Critical partition rule

Related records must not cross partitions:

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

The framework should also audit shared IDs, exact duplicates, near-duplicates, and target-code support.

## External-replication workflow

```text
provide data + tariff hierarchy + normative corpus
  -> map logical fields
  -> validate hierarchy and normative coverage
  -> create/validate independent splits
  -> audit dependence and duplicates
  -> historical retrieval
  -> normative retrieval
  -> optional integration / LLM
  -> evaluation + error analysis + reproducibility manifest
```

## Target quick start

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.example.yaml
python scripts/run_experiment.py --config configs/examples/custom_dataset.example.yaml
```

A reference study will be reproduced through its own versioned preset rather than a hard-coded chapter-specific interface.

## Methodologically valid replication

A replication should explicitly define the nomenclature and target level, use compatible labels and normative evidence, preserve group independence, isolate evaluation labels, compare methods on the same evaluation instances, avoid tuning on final evaluation data, record hashes/provenance, and report historical/normative coverage limitations.

## Documentation

See `docs/USING_YOUR_OWN_DATA.md`, `docs/DATA_CONTRACT.md`, `docs/TAXONOMY_AND_NORMATIVE_CORPUS.md`, `docs/EXPERIMENT_PROTOCOL.md`, `docs/REPRODUCIBILITY.md`, `docs/DATA_PROVENANCE.md`, and `docs/EXPECTED_RESULTS.md`.

---

# Français

## Qu'est-ce que ce dépôt ?

Ce dépôt fournit une implémentation reproductible et configurable d'un **protocole expérimental de recommandation auditable de classification tarifaire hiérarchique**. Il combine la recherche dans un historique de classifications, la recherche de preuves normatives et, de manière optionnelle, une génération contrôlée avec un LLM local.

Le cadre **n'est pas limité à un chapitre, une classe, un pays, une longueur de code fixe ou à NANDINA**. Il peut être utilisé sur un seul chapitre, plusieurs chapitres ou l'ensemble de l'univers tarifaire représenté dans les données. Le niveau cible peut être HS-2, HS-4, HS-6, une extension régionale ou nationale à 8 chiffres, une sous-position nationale à 10 chiffres ou une autre extension hiérarchique compatible.

Il ne produit pas de classement douanier juridiquement contraignant et ne remplace pas l'expertise humaine.

## Modes d'utilisation

| Mode | Données | Objectif |
|---|---|---|
| `reference` | Données/configuration figées | Reproduire une étude expérimentale de référence |
| `custom` | Données et corpus de l'utilisateur | Répliquer le protocole sur des données indépendantes |
| `synthetic` | Données de démonstration | Vérifier l'installation et le flux |

## Hiérarchie tarifaire configurable

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null   # tous les chapitres présents dans les données
```

La restriction à un ou plusieurs chapitres est une décision expérimentale facultative, et non une limitation du cadre.

## Corpus normatif configurable

Les preuves normatives doivent être versionnées et compatibles avec la juridiction, la nomenclature et le niveau cible. Le corpus peut combiner la nomenclature HS internationale, les notes de sections et de chapitres, les règles d'interprétation, des extensions régionales, la nomenclature tarifaire nationale et des notes complémentaires nationales.

```yaml
normative:
  enabled: true
  corpus_path: data/normative/chile_2026/
  jurisdiction: CL
  nomenclature: national_tariff
  supported_digits: [2, 4, 6, 8, 10]
```

Si la cible est à 10 chiffres mais que le corpus normatif ne couvre que 8 chiffres, cette limite de couverture doit être signalée explicitement.

## Utiliser ses propres données

Les rôles logiques requis sont `analysis_id`, `description`, `label` et, de préférence, `group_id` lorsque plusieurs observations dépendent d'une même entité supérieure. Les noms de colonnes sont configurables.

```yaml
experiment:
  id: customs_replication
  mode: custom

dataset:
  path: data/customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: tariff_code

classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null
```

## Règle critique de partitionnement

Les enregistrements dépendants ne doivent pas traverser les partitions :

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

Le cadre doit également auditer les identifiants partagés, les doublons exacts, les quasi-doublons et le support historique des codes cibles.

## Flux de réplication externe

```text
fournir données + hiérarchie tarifaire + corpus normatif
  -> mapper les champs logiques
  -> valider la hiérarchie et la couverture normative
  -> créer/valider des partitions indépendantes
  -> auditer dépendances et doublons
  -> recherche historique
  -> recherche normative
  -> intégration / LLM optionnel
  -> évaluation + analyse d'erreurs + manifeste de reproductibilité
```

## Démarrage rapide cible

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.example.yaml
python scripts/run_experiment.py --config configs/examples/custom_dataset.example.yaml
```

Une étude de référence sera reproduite via son propre preset versionné, sans interface codée en dur pour un chapitre particulier.

## Réplication méthodologiquement valide

Une réplication doit définir explicitement la nomenclature et le niveau cible, utiliser des étiquettes et des preuves normatives compatibles, préserver l'indépendance des groupes, isoler les vraies étiquettes d'évaluation, comparer les méthodes sur les mêmes instances, éviter l'ajustement sur l'évaluation finale, enregistrer les hashes et la provenance, et déclarer les limites de couverture historique ou normative.

## Documentation

Voir `docs/USING_YOUR_OWN_DATA.md`, `docs/DATA_CONTRACT.md`, `docs/TAXONOMY_AND_NORMATIVE_CORPUS.md`, `docs/EXPERIMENT_PROTOCOL.md`, `docs/REPRODUCIBILITY.md`, `docs/DATA_PROVENANCE.md` et `docs/EXPECTED_RESULTS.md`.
