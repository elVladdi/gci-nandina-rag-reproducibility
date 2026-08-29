# gci-nandina-rag-reproducibility

**Experimental framework for reproducible and auditable NANDINA/HS-code recommendation**

[Español](#español) · [English](#english) · [Français](#français)

Repositorio de desarrollo relacionado / Related development repository / Dépôt de développement associé: [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag)

---

# Español

## ¿Qué es este repositorio?

Este repositorio proporciona una **implementación reproducible y parametrizable de un protocolo experimental** para evaluar recomendación auditable de códigos NANDINA/HS mediante:

```text
Descripción comercial
        │
        ▼
Recuperación histórica
        │
        ▼
Candidatos HS/NANDINA
        │
        ├──────────────► Precedentes históricos
        │
        └──────────────► Evidencia normativa
                         │
                         ▼
                 Constructor de contexto
                         │
                         ▼
                     LLM local
                         │
                         ▼
                  Salida auditable
```

El objetivo no es emitir una clasificación aduanera vinculante ni sustituir el criterio experto. El objetivo es **evaluar de forma reproducible un procedimiento de recuperación, comparación, evidencia documental y explicación controlada**.

## Dos modos de uso

| Modo | Datos | Objetivo |
|---|---|---|
| `reference` | Dataset y configuraciones congeladas | Reproducir un estudio experimental de referencia y verificar sus resultados |
| `custom` | Dataset proporcionado por el usuario | Replicar el mismo protocolo experimental sobre datos independientes |
| `synthetic` | Dataset demostrativo incluido | Verificar instalación, configuración y flujo completo sin datos sensibles |

> Cambiar el dataset constituye una **replicación externa del protocolo**, no una reproducción exacta de los valores numéricos del estudio de referencia.

## ¿Puedo usar mis propios datos?

**Sí.** El framework está diseñado para aceptar datasets aduaneros compatibles sin exigir los mismos nombres de columnas, país, capítulo HS, tamaño de muestra ni distribución de códigos del estudio de referencia.

El usuario mapea sus columnas a roles lógicos:

| Rol lógico | Obligatorio | Significado |
|---|---:|---|
| `analysis_id` | Sí | Identificador único de la unidad evaluada, por ejemplo una línea o ítem de declaración |
| `group_id` | Recomendado | Unidad superior cuyos registros relacionados no deben cruzar particiones, por ejemplo una declaración |
| `description` | Sí | Descripción comercial utilizada como consulta |
| `label` | Sí | Código HS/NANDINA de referencia |
| `chapter` | No | Capítulo HS, si no se deriva del código |
| `heading` | No | Partida HS-4, si no se deriva del código |
| `hs6` | No | HS-6, si no se deriva del código |

Ejemplo mínimo:

```csv
item_id,declaration_id,product_description,hs_code
CL-0001,D-001,"AUTOMOVIL GASOLINA 1500 CC",870322
CL-0002,D-001,"VEHICULO DE PASAJEROS MOTOR 1600 CC",870323
CL-0003,D-002,"CAMION DIESEL 5 TONELADAS",870421
```

Configuración correspondiente:

```yaml
experiment:
  id: chile_class87
  mode: custom

dataset:
  path: data/chile_customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: hs_code

classification:
  target_level: hs6

split:
  strategy: grouped
  train_ratio: 0.70
  dev_ratio: 0.10
  eval_ratio: 0.20
  seed: 2026
```

Los nombres de columnas son configurables. La lógica experimental no debe depender de nombres específicos del dataset de referencia.

## Regla crítica: evitar dependencia entre particiones

Si varias filas pertenecen a una misma declaración, transacción, embarque, factura u otra unidad superior, **todos los registros de esa unidad deben permanecer en la misma partición**.

No se recomienda dividir filas aleatoriamente cuando existen observaciones relacionadas.

El framework debe poder verificar:

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

Esta regla evita que el desempeño aparente se beneficie de dependencia estructural entre el banco histórico y el conjunto de evaluación.

## Quick start previsto

```bash
git clone https://github.com/elVladdi/gci-nandina-rag-reproducibility.git
cd gci-nandina-rag-reproducibility
python -m venv .venv
```

Linux/macOS:

```bash
source .venv/bin/activate
```

Windows:

```powershell
.venv\Scripts\activate
```

Instalación:

```bash
pip install -r requirements.txt
```

Validación de un dataset propio:

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.yaml
```

Ejecución de una replicación:

```bash
python scripts/run_experiment.py --config configs/examples/custom_dataset.yaml
```

Reproducción del estudio de referencia:

```bash
python scripts/reproduce_all.py --config configs/presets/reference_class87_v0.2.yaml
```

> Estos entry points forman parte de la interfaz objetivo y se incorporarán progresivamente a medida que se congelen los componentes experimentales correspondientes.

## ¿Qué produce una ejecución?

La estructura objetivo es:

```text
results/<experiment_id>/
├── manifest.json
├── dataset_audit.json
├── split_summary.json
├── historical_retrieval/
│   ├── ranking.csv
│   ├── metrics.json
│   └── errors.csv
├── normative_retrieval/
│   ├── ranking.csv
│   └── metrics.json
├── candidate_pool/
│   └── candidates.csv
├── explanations/
│   └── top3_explanations.jsonl
└── reports/
    └── summary.md
```

Cada ejecución debe registrar versiones, configuración, hashes de entradas y outputs, commit de código, entorno y parámetros relevantes.

## Métricas previstas

Según la fase ejecutada, el protocolo puede reportar:

- Top-1, Top-3, Top-5, Top-10 y Top-50;
- MRR;
- cobertura normativa;
- posición del código verdadero;
- errores por nivel jerárquico HS;
- desempeño según soporte histórico;
- sensibilidad a duplicados y near-duplicates;
- métricas de candidate pools;
- comparación antes/después de reranking diagnóstico;
- validaciones estructurales de explicaciones Top-3.

## ¿Cuándo una replicación es metodológicamente válida?

Una replicación debe, como mínimo:

- utilizar unidades de análisis identificables y únicas;
- impedir que grupos relacionados crucen particiones cuando exista una unidad superior compartida;
- impedir que la etiqueta de evaluación entre como información de consulta;
- evaluar las estrategias comparadas sobre exactamente las mismas instancias finales;
- registrar versiones de datasets y corpus;
- preservar configuración y hashes cuando corresponda;
- no ajustar hiperparámetros sobre el conjunto final de evaluación;
- permitir recalcular las métricas desde resultados por caso;
- documentar cualquier desviación respecto del protocolo de referencia.

## ¿Qué puede cambiar?

| Elemento | Replicación externa |
|---|---|
| País o administración aduanera | Sí |
| Dataset | Sí |
| Capítulo HS | Sí |
| Tamaño de muestra | Sí |
| Nombres de columnas | Sí |
| Banco histórico | Sí |
| Corpus normativo | Sí |
| Extensión nacional del HS | Sí, si se configura y documenta |
| Modelo LLM local | Sí, si se registra |
| Proporciones del split | Sí |
| Regla de partición segura por grupo | Debe preservarse cuando exista dependencia grupal |
| Mismo evalset para estrategias comparadas | Debe preservarse |
| Controles de leakage | Deben preservarse |
| Definiciones de métricas | Se recomienda preservarlas para comparabilidad |

## HS internacional, NANDINA y extensiones nacionales

El framework distingue entre diferentes niveles de nomenclatura:

- niveles armonizados internacionalmente del HS;
- extensiones regionales como NANDINA;
- extensiones arancelarias nacionales.

Un usuario externo debe declarar el nivel objetivo y proporcionar el corpus normativo correspondiente. Por ejemplo:

```yaml
classification:
  target_level: hs6
```

u otra extensión nacional documentada.

## Qué no hace este framework

Este framework:

- no emite decisiones aduaneras jurídicamente vinculantes;
- no sustituye especialistas en clasificación;
- no garantiza el mismo desempeño en distintas jurisdicciones;
- no supone que extensiones NANDINA sean aplicables fuera de su jurisdicción;
- no trata la salida de un LLM como verdad de referencia;
- no convierte una replicación con datos propios en una reproducción exacta del estudio de referencia.

## Estado de la implementación de referencia

La implementación de referencia en desarrollo utiliza actualmente un piloto Clase 87 con partición segura por declaración. Algunos componentes ya están cerrados y otros permanecen en construcción. Este estado se mantiene como **trazabilidad interna del estudio de referencia**, no como requisito para usuarios externos.

El desarrollo activo permanece en [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag).

## Documentación

- [`docs/USING_YOUR_OWN_DATA.md`](docs/USING_YOUR_OWN_DATA.md) — replicación con datasets externos.
- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md) — contrato lógico de datos.
- [`docs/EXPERIMENT_PROTOCOL.md`](docs/EXPERIMENT_PROTOCOL.md) — etapas canónicas del protocolo.
- [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md) — niveles y criterios de reproducibilidad.
- [`docs/DATA_PROVENANCE.md`](docs/DATA_PROVENANCE.md) — procedencia y redistribución.
- [`docs/EXPECTED_RESULTS.md`](docs/EXPECTED_RESULTS.md) — resultados esperados para presets de referencia.

---

# English

## What is this repository?

This repository provides a **reproducible and configurable implementation of an experimental protocol** for evaluating auditable NANDINA/HS-code recommendation through historical retrieval, normative evidence retrieval, and controlled local-LLM generation.

```text
Commercial description
        │
        ▼
Historical retrieval
        │
        ▼
HS/NANDINA candidates
        │
        ├──────────────► Historical precedents
        │
        └──────────────► Normative evidence
                         │
                         ▼
                   Context builder
                         │
                         ▼
                     Local LLM
                         │
                         ▼
                   Auditable output
```

The framework does not issue legally binding customs classifications and is not intended to replace expert review. Its purpose is to **evaluate a reproducible procedure for retrieval, comparison, documentary evidence, and controlled explanation**.

## Usage modes

| Mode | Data | Goal |
|---|---|---|
| `reference` | Frozen dataset and configurations | Reproduce a reference experimental study and verify its results |
| `custom` | User-provided dataset | Replicate the experimental protocol on independent data |
| `synthetic` | Included demonstration dataset | Test installation and the full workflow without sensitive data |

> Using a different dataset constitutes an **external replication of the protocol**, not an exact reproduction of the numerical results from the reference study.

## Can I use my own data?

**Yes.** The framework is intended to accept compatible customs datasets without requiring the same column names, country, HS chapter, sample size, or code distribution as the reference study.

Users map their columns to logical roles:

| Logical role | Required | Meaning |
|---|---:|---|
| `analysis_id` | Yes | Unique identifier of the evaluated item or declaration line |
| `group_id` | Recommended | Higher-level entity whose related records must not cross partitions, e.g. a declaration |
| `description` | Yes | Commercial description used as the retrieval query |
| `label` | Yes | Ground-truth HS/NANDINA code |
| `chapter` | No | HS chapter if not derived from the code |
| `heading` | No | HS-4 heading if not derived from the code |
| `hs6` | No | HS-6 code if not derived from the label |

Minimal example:

```csv
item_id,declaration_id,product_description,hs_code
CL-0001,D-001,"GASOLINE PASSENGER CAR 1500 CC",870322
CL-0002,D-001,"PASSENGER VEHICLE ENGINE 1600 CC",870323
CL-0003,D-002,"DIESEL TRUCK 5 TONNES",870421
```

Configuration:

```yaml
experiment:
  id: chile_class87
  mode: custom

dataset:
  path: data/chile_customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: hs_code

classification:
  target_level: hs6

split:
  strategy: grouped
  train_ratio: 0.70
  dev_ratio: 0.10
  eval_ratio: 0.20
  seed: 2026
```

Column names are configurable. The experimental logic must not depend on reference-dataset-specific names.

## Critical rule: prevent cross-partition dependence

If several rows belong to the same declaration, transaction, shipment, invoice, or other higher-level entity, **all records from that entity should remain in the same partition**.

Random row-level splitting is inappropriate when related observations share a common parent entity.

The framework should be able to enforce:

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

This prevents apparent performance from being inflated by structural dependence between the historical bank and evaluation set.

## Planned quick start

```bash
git clone https://github.com/elVladdi/gci-nandina-rag-reproducibility.git
cd gci-nandina-rag-reproducibility
python -m venv .venv
pip install -r requirements.txt
```

Validate your dataset:

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.yaml
```

Run an external replication:

```bash
python scripts/run_experiment.py --config configs/examples/custom_dataset.yaml
```

Reproduce the reference study:

```bash
python scripts/reproduce_all.py --config configs/presets/reference_class87_v0.2.yaml
```

> These commands describe the target interface and will be added progressively as the corresponding experimental components are frozen.

## Expected outputs

```text
results/<experiment_id>/
├── manifest.json
├── dataset_audit.json
├── split_summary.json
├── historical_retrieval/
│   ├── ranking.csv
│   ├── metrics.json
│   └── errors.csv
├── normative_retrieval/
│   ├── ranking.csv
│   └── metrics.json
├── candidate_pool/
│   └── candidates.csv
├── explanations/
│   └── top3_explanations.jsonl
└── reports/
    └── summary.md
```

Each run should record input/output hashes, configuration, code commit, environment, model identifiers where applicable, and relevant parameters.

## When is a replication methodologically valid?

At minimum, a replication should:

- use unique and identifiable analysis units;
- prevent related groups from crossing partitions when a shared parent entity exists;
- prevent evaluation labels from entering the retrieval query;
- evaluate compared strategies on exactly the same final evaluation instances;
- record dataset and corpus versions;
- preserve configuration and hashes where applicable;
- avoid tuning on the final evaluation set;
- allow metrics to be recomputed from case-level outputs;
- document deviations from the reference protocol.

## What can be changed?

| Element | External replication |
|---|---|
| Country/customs administration | Yes |
| Dataset | Yes |
| HS chapter | Yes |
| Sample size | Yes |
| Column names | Yes |
| Historical bank | Yes |
| Normative corpus | Yes |
| National tariff extension | Yes, if configured and documented |
| Local LLM | Yes, if recorded |
| Split proportions | Yes |
| Group-safe partition rule | Should remain when grouped dependence exists |
| Same evaluation cases across compared methods | Must remain |
| Leakage controls | Must remain |
| Metric definitions | Preferably retained for comparability |

## International HS, NANDINA, and national extensions

The framework distinguishes between internationally harmonized HS levels, regional extensions such as NANDINA, and national tariff extensions. External users must declare the target classification level and provide the corresponding normative corpus.

## What this framework does not do

It does not:

- issue legally binding customs classifications;
- replace customs classification specialists;
- guarantee identical performance across jurisdictions;
- assume NANDINA extensions apply outside their jurisdiction;
- treat LLM output as ground truth;
- treat a custom-data replication as an exact reproduction of the reference study.

## Reference implementation status

The current reference implementation uses a Class 87 pilot with declaration-safe partitioning. Some experimental components are frozen while others remain under development. This information exists for **reference-study traceability** and is not a requirement for external users.

Active development remains in [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag).

## Documentation

- [`docs/USING_YOUR_OWN_DATA.md`](docs/USING_YOUR_OWN_DATA.md) — external datasets.
- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md) — logical data contract.
- [`docs/EXPERIMENT_PROTOCOL.md`](docs/EXPERIMENT_PROTOCOL.md) — canonical experimental stages.
- [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md) — reproducibility levels and criteria.
- [`docs/DATA_PROVENANCE.md`](docs/DATA_PROVENANCE.md) — data and corpus provenance.
- [`docs/EXPECTED_RESULTS.md`](docs/EXPECTED_RESULTS.md) — expected results for reference presets.

---

# Français

## Qu'est-ce que ce dépôt ?

Ce dépôt fournit une **implémentation reproductible et configurable d'un protocole expérimental** destiné à évaluer la recommandation auditable de codes NANDINA/SH à partir de la recherche dans des précédents historiques, de preuves normatives et d'une génération contrôlée par un LLM local.

```text
Description commerciale
        │
        ▼
Recherche historique
        │
        ▼
Candidats SH/NANDINA
        │
        ├──────────────► Précédents historiques
        │
        └──────────────► Preuves normatives
                         │
                         ▼
                Construction du contexte
                         │
                         ▼
                      LLM local
                         │
                         ▼
                  Sortie auditable
```

Le framework ne produit pas de classement douanier juridiquement contraignant et ne remplace pas l'expertise humaine. Son objectif est **d'évaluer de manière reproductible une procédure de recherche, comparaison, justification documentaire et explication contrôlée**.

## Modes d'utilisation

| Mode | Données | Objectif |
|---|---|---|
| `reference` | Jeu de données et configurations gelés | Reproduire une étude expérimentale de référence et vérifier ses résultats |
| `custom` | Données fournies par l'utilisateur | Répliquer le même protocole expérimental sur des données indépendantes |
| `synthetic` | Jeu de données de démonstration | Vérifier l'installation et le workflow sans données sensibles |

> L'utilisation d'un autre jeu de données constitue une **réplication externe du protocole**, et non une reproduction exacte des résultats numériques de l'étude de référence.

## Puis-je utiliser mes propres données ?

**Oui.** Le framework est conçu pour accepter des données douanières compatibles sans imposer les mêmes noms de colonnes, le même pays, le même chapitre SH, la même taille d'échantillon ou la même distribution de codes que l'étude de référence.

Les colonnes sont associées à des rôles logiques :

| Rôle logique | Obligatoire | Signification |
|---|---:|---|
| `analysis_id` | Oui | Identifiant unique de l'article ou de la ligne de déclaration évaluée |
| `group_id` | Recommandé | Entité supérieure dont les enregistrements liés ne doivent pas traverser les partitions, par ex. une déclaration |
| `description` | Oui | Description commerciale utilisée comme requête |
| `label` | Oui | Code SH/NANDINA de référence |
| `chapter` | Non | Chapitre SH s'il n'est pas dérivé du code |
| `heading` | Non | Position SH-4 si elle n'est pas dérivée du code |
| `hs6` | Non | Code SH-6 s'il n'est pas dérivé du label |

Exemple minimal :

```csv
item_id,declaration_id,product_description,hs_code
CL-0001,D-001,"VOITURE ESSENCE 1500 CC",870322
CL-0002,D-001,"VEHICULE DE TOURISME MOTEUR 1600 CC",870323
CL-0003,D-002,"CAMION DIESEL 5 TONNES",870421
```

Configuration :

```yaml
experiment:
  id: chile_class87
  mode: custom

dataset:
  path: data/chile_customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: hs_code

classification:
  target_level: hs6

split:
  strategy: grouped
  train_ratio: 0.70
  dev_ratio: 0.10
  eval_ratio: 0.20
  seed: 2026
```

Les noms de colonnes sont configurables. La logique expérimentale ne doit pas dépendre des noms utilisés dans le jeu de données de référence.

## Règle critique : éviter la dépendance entre partitions

Si plusieurs lignes appartiennent à la même déclaration, transaction, expédition, facture ou autre entité supérieure, **tous les enregistrements de cette entité doivent rester dans la même partition**.

Il ne faut pas effectuer un découpage aléatoire ligne par ligne lorsque des observations liées partagent une même entité parente.

Le framework doit pouvoir imposer :

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

Cette règle évite qu'une dépendance structurelle entre la banque historique et l'ensemble d'évaluation ne gonfle artificiellement les performances apparentes.

## Démarrage rapide prévu

```bash
git clone https://github.com/elVladdi/gci-nandina-rag-reproducibility.git
cd gci-nandina-rag-reproducibility
python -m venv .venv
pip install -r requirements.txt
```

Valider ses propres données :

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.yaml
```

Exécuter une réplication externe :

```bash
python scripts/run_experiment.py --config configs/examples/custom_dataset.yaml
```

Reproduire l'étude de référence :

```bash
python scripts/reproduce_all.py --config configs/presets/reference_class87_v0.2.yaml
```

> Ces commandes décrivent l'interface cible et seront ajoutées progressivement à mesure que les composants expérimentaux correspondants seront gelés.

## Sorties attendues

```text
results/<experiment_id>/
├── manifest.json
├── dataset_audit.json
├── split_summary.json
├── historical_retrieval/
│   ├── ranking.csv
│   ├── metrics.json
│   └── errors.csv
├── normative_retrieval/
│   ├── ranking.csv
│   └── metrics.json
├── candidate_pool/
│   └── candidates.csv
├── explanations/
│   └── top3_explanations.jsonl
└── reports/
    └── summary.md
```

Chaque exécution doit enregistrer les versions, les hashes d'entrée et de sortie, la configuration, le commit du code, l'environnement et les paramètres pertinents.

## Quand une réplication est-elle méthodologiquement valide ?

Au minimum, une réplication doit :

- utiliser des unités d'analyse uniques et identifiables ;
- empêcher les groupes liés de traverser les partitions lorsqu'une entité parente commune existe ;
- empêcher le label d'évaluation d'entrer dans la requête de recherche ;
- évaluer les méthodes comparées exactement sur les mêmes instances finales ;
- enregistrer les versions des datasets et corpus ;
- préserver les configurations et les hashes lorsque cela s'applique ;
- éviter tout réglage d'hyperparamètres sur l'ensemble final d'évaluation ;
- permettre le recalcul des métriques à partir des sorties au niveau des cas ;
- documenter tout écart par rapport au protocole de référence.

## Que peut-on modifier ?

| Élément | Réplication externe |
|---|---|
| Pays / administration douanière | Oui |
| Dataset | Oui |
| Chapitre SH | Oui |
| Taille de l'échantillon | Oui |
| Noms des colonnes | Oui |
| Banque historique | Oui |
| Corpus normatif | Oui |
| Extension tarifaire nationale | Oui, si configurée et documentée |
| LLM local | Oui, s'il est enregistré |
| Proportions du split | Oui |
| Règle de partition sécurisée par groupe | À conserver lorsqu'une dépendance groupée existe |
| Mêmes cas d'évaluation pour les méthodes comparées | À conserver |
| Contrôles de fuite d'information | À conserver |
| Définitions des métriques | De préférence à conserver pour la comparabilité |

## SH international, NANDINA et extensions nationales

Le framework distingue les niveaux harmonisés internationalement du SH, les extensions régionales comme NANDINA et les extensions tarifaires nationales. Un utilisateur externe doit déclarer le niveau de classification cible et fournir le corpus normatif correspondant.

## Ce que le framework ne fait pas

Il ne :

- produit pas de classement douanier juridiquement contraignant ;
- remplace pas les spécialistes de la classification ;
- garantit pas des performances identiques entre juridictions ;
- suppose pas que les extensions NANDINA s'appliquent hors de leur juridiction ;
- considère pas la sortie d'un LLM comme vérité de référence ;
- considère pas une réplication sur données propres comme une reproduction exacte de l'étude de référence.

## État de l'implémentation de référence

L'implémentation de référence actuelle utilise un pilote de la Classe 87 avec partitionnement sécurisé par déclaration. Certains composants expérimentaux sont gelés tandis que d'autres sont encore en développement. Ces informations servent uniquement à la **traçabilité de l'étude de référence** et ne constituent pas des exigences pour les utilisateurs externes.

Le développement actif reste dans [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag).

## Documentation

- [`docs/USING_YOUR_OWN_DATA.md`](docs/USING_YOUR_OWN_DATA.md) — utilisation de datasets externes.
- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md) — contrat logique des données.
- [`docs/EXPERIMENT_PROTOCOL.md`](docs/EXPERIMENT_PROTOCOL.md) — étapes canoniques du protocole expérimental.
- [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md) — niveaux et critères de reproductibilité.
- [`docs/DATA_PROVENANCE.md`](docs/DATA_PROVENANCE.md) — provenance des données et corpus.
- [`docs/EXPECTED_RESULTS.md`](docs/EXPECTED_RESULTS.md) — résultats attendus pour les presets de référence.

---

## Repository status

This repository is currently an evolving reproducibility/replication scaffold. The reusable framework, reference presets, synthetic example, validation commands, and stable end-to-end runners will be added as the corresponding experimental components are frozen and validated.

The design principle is simple:

> A new user should be able to understand what data are required, prevent invalid experimental splits, execute the protocol, inspect auditable outputs, and determine whether the replication was methodologically valid — without modifying the framework source code.
