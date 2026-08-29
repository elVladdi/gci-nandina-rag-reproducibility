# gci-nandina-rag-reproducibility

**Especificación reproducible para replicación independiente de experimentos de clasificación arancelaria auditable**  
**Reproducible specification for independent replication of auditable tariff-classification experiments**  
**Spécification reproductible pour la réplication indépendante d'expériences de classification tarifaire auditable**

[Español](#español) · [English](#english) · [Français](#français)

---

# Español

## Propósito

Este repositorio publica el **protocolo experimental, las especificaciones funcionales, los contratos de datos, las configuraciones de referencia, los artefactos verificables y los vectores de prueba** necesarios para que terceros puedan implementar y replicar independientemente una investigación experimental sobre recomendación auditable de clasificación arancelaria jerárquica.

**Este repositorio no distribuye la implementación de software de referencia.** Su objetivo es describir el procedimiento con suficiente precisión para permitir una implementación independiente y verificable.

La replicación puede utilizar datos propios, otra jurisdicción, uno o varios capítulos o el universo arancelario completo disponible, y distintos niveles de la jerarquía HS o de sus extensiones regionales/nacionales.

## Qué se publica y qué no

| Se publica | No se publica aquí |
|---|---|
| Protocolo experimental completo | Código fuente de la implementación de referencia |
| Especificaciones funcionales por componente | Scripts operativos de la implementación de referencia |
| Contrato de datos y configuración | Pipeline productivo |
| Pseudocódigo cuando sea necesario | Componentes de software reutilizables |
| Parámetros y decisiones experimentales | Implementación interna del framework |
| Esquemas de entradas y salidas | Datos restringidos o confidenciales |
| Manifests, hashes y procedencia | Material de terceros sin derechos de redistribución |
| Métricas y resultados verificables | |
| Vectores de prueba y ejemplos sintéticos | |

La especificación no debe ser deliberadamente vaga: cada componente deberá describirse con suficiente detalle para que un tercero pueda desarrollar su propia implementación y contrastarla contra entradas, salidas e invariantes publicados.

## Dos objetivos distintos

### Reproducción documental y verificable del estudio de referencia

Permite inspeccionar configuraciones congeladas, manifests, hashes, resultados esperados, métricas y decisiones metodológicas del estudio experimental de referencia.

### Replicación independiente

Permite que una organización implemente el protocolo con su propio software y lo aplique a sus propios datos, nomenclatura y corpus normativo.

Una réplica externa **no necesita utilizar el mismo capítulo, país, cantidad de dígitos ni distribución de datos del estudio de referencia**.

## Alcance de clasificación configurable

La especificación se basa en una jerarquía arancelaria configurable. Puede abarcar, según la nomenclatura disponible:

- HS-2: capítulo;
- HS-4: partida;
- HS-6: subpartida armonizada;
- extensiones regionales a 8 dígitos, como NANDINA cuando corresponda;
- subpartidas nacionales de 8, 10 u otra cantidad de dígitos;
- uno, varios o todos los capítulos presentes en los datos.

Ejemplo conceptual:

```yaml
classification:
  family: HS
  jurisdiction: CL
  nomenclature: national_tariff
  target_digits: 10
  hierarchy_digits: [2, 4, 6, 8, 10]

scope:
  chapters: null   # todos los capítulos disponibles
```

La selección de un capítulo particular es una decisión de diseño experimental, no una limitación del protocolo.

## El corpus normativo forma parte del experimento

El corpus normativo debe ser coherente con la jurisdicción, nomenclatura, versión temporal y profundidad objetivo. Puede incluir nomenclatura HS, Reglas Generales para la Interpretación, notas de sección/capítulo, extensiones regionales, aranceles nacionales, notas complementarias y otras fuentes normativas explícitamente declaradas.

La especificación exige registrar su procedencia, versión, cobertura jerárquica y hashes cuando corresponda.

Si el objetivo es clasificar a 10 dígitos pero la evidencia normativa disponible solo sustenta hasta 8, esa brecha debe declararse. Una explicación no debe atribuir a la evidencia normativa una precisión que el corpus no contiene.

## Datos propios

Una implementación independiente debe poder mapear los datos de origen a roles lógicos, sin exigir nombres de columnas específicos.

| Rol lógico | Obligatorio | Significado |
|---|---:|---|
| `analysis_id` | Sí | Identificador único de la instancia experimental |
| `description` | Sí | Descripción comercial utilizada como consulta |
| `label` | Sí | Código arancelario verdadero en el nivel objetivo |
| `group_id` | Cuando exista dependencia | Declaración, transacción, embarque u otra unidad superior relacionada |

Ejemplo:

```csv
item_id,declaration_id,product_description,tariff_code
CL-0001,D-001,"MOTOR ELECTRICO INDUSTRIAL",8501520010
CL-0002,D-001,"TABLERO DE CONTROL ELECTRICO",8537109090
CL-0003,D-002,"BOMBA CENTRIFUGA",8413700090
```

## Regla crítica de independencia

Cuando varias observaciones pertenecen a una misma unidad superior, esa unidad no debe cruzar las particiones experimentales:

```text
historical.group_id ∩ development.group_id = ∅
historical.group_id ∩ evaluation.group_id  = ∅
development.group_id ∩ evaluation.group_id = ∅
```

La implementación externa debe auditar además IDs compartidos, duplicados exactos, near-duplicates, cobertura histórica y cobertura normativa.

## Flujo de replicación independiente

```text
definir pregunta experimental
        ↓
proveer datos + taxonomía + corpus normativo
        ↓
mapear contrato de datos
        ↓
implementar/validar particionado independiente
        ↓
auditar dependencia y duplicados
        ↓
implementar recuperación histórica
        ↓
implementar recuperación normativa
        ↓
implementar integración / generación, si corresponde
        ↓
calcular métricas según especificación
        ↓
comparar con vectores de prueba e invariantes
        ↓
generar manifest de replicación
```

## Fichas de especificación

La estructura objetivo de `protocol/` documentará cada componente mediante una ficha independiente. Cada ficha debe contener, como mínimo:

1. propósito;
2. entradas;
3. precondiciones;
4. procedimiento o pseudocódigo;
5. parámetros configurables;
6. invariantes;
7. salidas;
8. validaciones;
9. métricas aplicables;
10. criterios de aceptación;
11. casos límite;
12. metadatos de reproducibilidad.

Las fichas previstas incluyen validación de datos, particionado group-safe, auditoría de duplicados, recuperación histórica, recuperación normativa plana y jerárquica, recuperación densa cuando corresponda, integración de candidatos, reranking diagnóstico, construcción de contexto, explicación controlada, evaluación y generación de manifests.

## Vectores de prueba

Además de las especificaciones textuales, se publicarán casos de entrada/salida verificables para comprobar implementaciones independientes.

Conceptualmente:

```text
entrada publicada
      ↓
implementación independiente
      ↓
salida obtenida
      ↓
comparación con salida/invariantes esperados
```

Los vectores de prueba no sustituyen la replicación con datos reales, pero permiten verificar que componentes críticos interpretan correctamente la especificación.

## ¿Cuándo una réplica es metodológicamente válida?

Como mínimo debe:

- declarar nomenclatura, jurisdicción, versión y nivel objetivo;
- documentar el corpus normativo y su cobertura;
- preservar la independencia de unidades relacionadas entre particiones;
- impedir que las etiquetas verdaderas entren en consultas, ranking o generación;
- comparar estrategias sobre las mismas instancias cuando se formulen comparaciones directas;
- evitar ajuste de parámetros sobre el conjunto final de evaluación;
- registrar configuración, hashes, versiones, semillas y procedencia;
- conservar resultados por caso suficientes para recalcular métricas;
- documentar cobertura histórica y normativa;
- declarar cualquier desviación respecto del protocolo de referencia.

## Estructura objetivo

```text
gci-nandina-rag-reproducibility/
├── README.md
├── protocol/                 # fichas técnicas del procedimiento
│   ├── data/
│   ├── splitting/
│   ├── retrieval/
│   ├── ranking/
│   ├── rag/
│   ├── evaluation/
│   └── audit/
├── specifications/           # contratos y esquemas formales
│   ├── data_contract/
│   ├── configuration/
│   ├── inputs_outputs/
│   └── manifests/
├── reference_experiment/     # configuración y evidencia congelada
│   ├── configuration/
│   ├── manifests/
│   ├── expected_metrics/
│   └── expected_results/
├── test_vectors/             # entradas/salidas para validar implementaciones
├── examples/
│   └── synthetic/
└── docs/
    ├── REPLICATION_GUIDE.md
    ├── TAXONOMY_AND_NORMATIVE_CORPUS.md
    └── METHODOLOGICAL_VALIDITY.md
```

## Estado actual

El repositorio se encuentra en construcción. Las especificaciones se incorporarán progresivamente a medida que los componentes del estudio experimental de referencia queden metodológicamente cerrados. No debe interpretarse la ausencia temporal de una ficha como una especificación implícita.

---

# English

## Purpose

This repository publishes the **experimental protocol, functional specifications, data contracts, reference configurations, verifiable artifacts, and test vectors** required for third parties to independently implement and replicate experimental research on auditable hierarchical tariff-classification recommendation.

**The reference software implementation is not distributed in this repository.** The objective is to describe the procedure precisely enough to support an independent and verifiable implementation.

External replications may use their own data, jurisdiction, one or multiple chapters or the full tariff universe, and different HS or regional/national extension depths.

## What is published

Published materials include the complete experimental protocol, component specifications, data/configuration contracts, pseudocode where required, experimental parameters, input/output schemas, manifests, hashes, provenance, expected metrics, test vectors, and synthetic examples. Reference source code, operational scripts, production pipelines, restricted data, and non-redistributable third-party material are not published here.

## Independent replication

An external organization can implement the protocol in its own software and apply it to its own customs data, tariff nomenclature, and normative corpus. It does **not** need to reproduce the reference experiment's chapter, jurisdiction, code depth, sample size, or data distribution.

## Configurable tariff hierarchy and normative evidence

The protocol supports HS-2, HS-4, HS-6, regional extensions such as NANDINA where applicable, national tariff extensions such as 8- or 10-digit lines, and experiments spanning one, several, or all available chapters.

Normative evidence is an experimental input. Its jurisdiction, nomenclature, temporal version, hierarchical coverage, provenance, and hashes must be documented. Evidence limitations must never be hidden by the explanation stage.

## Core data roles

Independent implementations map local columns to `analysis_id`, `description`, `label`, and `group_id` when observations share a dependent parent entity. Column names are not prescribed.

## Critical independence rule

Related observations must not cross experimental partitions. Implementations should additionally audit shared IDs, exact and near duplicates, historical support, and normative coverage.

## Specification sheets

Each component specification should define purpose, inputs, preconditions, procedure/pseudocode, configurable parameters, invariants, outputs, validation, metrics, acceptance criteria, edge cases, and reproducibility metadata.

## Test vectors

Published input/output cases and invariants will allow independent implementations to verify critical components without receiving the reference source code.

## Valid replication

A methodologically valid replication documents taxonomy and normative evidence, preserves group independence and label isolation, uses comparable evaluation cases for direct method comparisons, avoids final-evaluation tuning, records hashes/provenance/seeds, preserves case-level results, and explicitly reports deviations and coverage limitations.

---

# Français

## Objectif

Ce dépôt publie le **protocole expérimental, les spécifications fonctionnelles, les contrats de données, les configurations de référence, les artefacts vérifiables et les vecteurs de test** nécessaires pour permettre à des tiers d'implémenter et de répliquer indépendamment une recherche expérimentale sur la recommandation auditable de classification tarifaire hiérarchique.

**L'implémentation logicielle de référence n'est pas distribuée dans ce dépôt.** L'objectif est de décrire la procédure avec une précision suffisante pour permettre une implémentation indépendante et vérifiable.

Une réplication externe peut utiliser ses propres données, sa juridiction, un ou plusieurs chapitres ou l'ensemble de l'univers tarifaire, ainsi que différents niveaux du SH ou de ses extensions régionales/nationales.

## Ce qui est publié

Le dépôt publie le protocole expérimental complet, les spécifications par composant, les contrats de données et de configuration, le pseudocode lorsque nécessaire, les paramètres expérimentaux, les schémas d'entrée/sortie, les manifests, les empreintes cryptographiques, la provenance, les métriques attendues, les vecteurs de test et des exemples synthétiques. Le code source de référence, les scripts opérationnels, les pipelines de production, les données restreintes et les contenus tiers non redistribuables ne sont pas publiés ici.

## Réplication indépendante

Une organisation externe peut implémenter le protocole avec son propre logiciel et l'appliquer à ses propres données douanières, sa nomenclature tarifaire et son corpus normatif. Elle n'est pas tenue de reproduire le chapitre, la juridiction, la profondeur du code, la taille de l'échantillon ou la distribution des données de l'expérience de référence.

## Hiérarchie et preuves normatives configurables

Le protocole couvre HS/SH-2, HS/SH-4, HS/SH-6, les extensions régionales telles que NANDINA lorsqu'elles sont applicables, les extensions tarifaires nationales à 8 ou 10 chiffres, ainsi que des expériences portant sur un, plusieurs ou tous les chapitres disponibles.

Le corpus normatif constitue une entrée expérimentale. Sa juridiction, sa nomenclature, sa version temporelle, sa couverture hiérarchique, sa provenance et ses empreintes doivent être documentées. Les limites de preuve ne doivent jamais être masquées par l'étape d'explication.

## Rôles de données fondamentaux

Les implémentations indépendantes associent leurs colonnes locales aux rôles `analysis_id`, `description`, `label` et `group_id` lorsque plusieurs observations partagent une unité supérieure dépendante. Aucun nom de colonne source n'est imposé.

## Règle critique d'indépendance

Les observations liées ne doivent pas traverser les partitions expérimentales. Les implémentations doivent également auditer les identifiants partagés, les doublons exacts et proches, le support historique et la couverture normative.

## Fiches de spécification

Chaque fiche doit définir l'objectif, les entrées, les préconditions, la procédure ou le pseudocode, les paramètres configurables, les invariants, les sorties, les validations, les métriques, les critères d'acceptation, les cas limites et les métadonnées de reproductibilité.

## Vecteurs de test

Des cas d'entrée/sortie et des invariants publiés permettront de vérifier les composants critiques d'une implémentation indépendante sans distribuer le code source de référence.

## Réplication valide

Une réplication méthodologiquement valide documente la taxonomie et les preuves normatives, préserve l'indépendance des groupes et l'isolation des étiquettes, utilise les mêmes cas d'évaluation pour les comparaisons directes, évite l'ajustement sur l'ensemble final d'évaluation, conserve les empreintes/provenance/graines et les résultats au niveau des cas, et déclare explicitement les écarts et limites de couverture.

---

## Related documentation / Documentación relacionada / Documentation associée

- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md)
- [`docs/TAXONOMY_AND_NORMATIVE_CORPUS.md`](docs/TAXONOMY_AND_NORMATIVE_CORPUS.md)
- [`docs/EXPERIMENT_PROTOCOL.md`](docs/EXPERIMENT_PROTOCOL.md)
- [`docs/DATA_PROVENANCE.md`](docs/DATA_PROVENANCE.md)

> Repository scope: **protocol and independent-replication specification, not distribution of the reference software implementation.**
