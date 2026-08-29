# gci-nandina-rag-reproducibility

Referencia ejecutable para reproducir y replicar un protocolo experimental de recomendación auditable de códigos NANDINA/HS mediante recuperación histórica, evidencia normativa y generación controlada con LLM local.

Repositorio de desarrollo relacionado: [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag).

## Propósito

Este repositorio tiene dos objetivos complementarios:

1. **Reproducción computacional del estudio experimental de referencia**: ejecutar configuraciones, datos y artefactos congelados para verificar resultados publicados del proyecto de investigación.
2. **Replicación con datos propios**: aplicar el mismo protocolo experimental a conjuntos de datos compatibles proporcionados por terceros, sin modificar el código fuente.

No es un segundo repositorio de desarrollo paralelo ni un clasificador arancelario universal. Su función es proporcionar una implementación reproducible, auditable y parametrizable del **protocolo experimental**.

## Modos de uso previstos

### Modo `reference`

Ejecuta un preset científico congelado con versiones, hashes y parámetros explícitos. Su finalidad es comprobar integridad de entradas, reproducir resultados y comparar salidas con valores esperados.

### Modo `custom`

Permite utilizar un dataset compatible propio. El usuario define mediante configuración los campos lógicos del experimento, por ejemplo:

```yaml
experiment:
  id: my_hs_experiment
  mode: custom

dataset:
  path: data/customs.csv
  analysis_unit: item_id
  grouping_unit: declaration_id
  description_column: product_description
  label_column: hs_code

split:
  strategy: grouped
  train_ratio: 0.70
  dev_ratio: 0.10
  eval_ratio: 0.20
```

Los nombres de columnas, clase arancelaria, tamaño del dataset y distribución de códigos no deben estar codificados de forma rígida en el framework.

## Estado actual

**Scaffold inicial.**

El desarrollo experimental continúa en `gci-nandina-rag`. Este repositorio aún no contiene la implementación completa de los runners canónicos ni todos los artefactos requeridos para una reproducción end-to-end.

Estado de referencia conocido al crear esta base:

- dataset experimental: `data_aduanas`, Clase 87, `v0.2`;
- estrategia de partición de referencia: `T5-safe-159`;
- unidad de análisis: `SERIE`;
- unidad de agrupamiento: `DECLARACION` / DAM;
- EXP-01 / EXP-02 / EXP-03: cerradas;
- Gate 5: cerrado;
- EXP-04 Fase A — BM25 histórico v0.2: completada;
- EXP-04 Fase B y posteriores: pendientes.

Estos valores describen la **instanciación experimental de referencia**. No son requisitos universales del framework.

## Principios

1. **Protocolo reutilizable.** El código canónico debe aceptar datasets compatibles mediante configuración.
2. **Preset de referencia congelado.** Los experimentos publicados deben poder reconstruirse con versiones y hashes explícitos.
3. **Separación entre framework y preset.** Las decisiones específicas de una campaña experimental no deben quedar codificadas como supuestos globales.
4. **Control de dependencia entre particiones.** Cuando varias instancias pertenecen a una misma unidad superior, el split debe poder agrupar por esa unidad para evitar dependencia cruzada.
5. **Hashes verificables.** Entradas y artefactos críticos de cada preset de referencia deben registrar SHA-256.
6. **Sin sobrescritura silenciosa.** Una versión publicada no debe reutilizar nombres para contenidos diferentes.
7. **Ejecución determinística cuando sea posible.** Semillas, configuración, entorno y comandos deben quedar documentados.
8. **Trazabilidad cruzada.** Cada release debe indicar el commit fuente del repositorio de desarrollo del que fue derivada.
9. **Validación previa de datos.** Un dataset externo no debe entrar al pipeline sin superar el contrato de datos y las verificaciones de integridad.
10. **No confundir replicación con igualdad de resultados.** Un dataset propio reproduce el procedimiento, no necesariamente los valores numéricos del estudio de referencia.

## Estructura

```text
.
|-- README.md
|-- .gitattributes
|-- .gitignore
|-- configs/
|   |-- presets/          # configuraciones congeladas de estudios de referencia
|   |-- examples/         # ejemplos para datasets externos
|   `-- schemas/          # contratos/esquemas de configuración
|-- data/                 # datos publicables, ejemplos o instrucciones de provisión
|-- docs/
|   |-- REPRODUCIBILITY.md
|   |-- DATA_PROVENANCE.md
|   |-- DATA_CONTRACT.md
|   |-- USING_YOUR_OWN_DATA.md
|   |-- EXPERIMENT_PROTOCOL.md
|   `-- EXPECTED_RESULTS.md
|-- framework/            # implementación canónica reutilizable, cuando se congele
|-- scripts/              # entry points de reproducción/replicación
|-- experiments/          # manifests de presets científicos congelados
|-- results/              # resultados esperados y artefactos verificables
|-- examples/             # datasets sintéticos o demostrativos
`-- tests/                # integridad, regresión y pruebas del protocolo
```

## Flujo previsto

### Reproducción del estudio de referencia

```text
validar entorno
  -> validar hashes y esquema
  -> cargar preset congelado
  -> ejecutar pipeline canónico
  -> regenerar métricas/artefactos
  -> comparar con resultados esperados
```

### Replicación con datos propios

```text
proveer dataset
  -> mapear columnas lógicas
  -> validar contrato de datos
  -> auditar dependencia/duplicados
  -> crear o validar particiones
  -> ejecutar recuperación/evaluación
  -> generar resultados y manifest propio
```

## Interfaces objetivo

Reproducción de referencia:

```bash
python scripts/reproduce_all.py --config configs/presets/reference_class87_v0.2.yaml
```

Replicación externa:

```bash
python scripts/validate_dataset.py --config configs/examples/custom_dataset.yaml
python scripts/run_experiment.py --config configs/examples/custom_dataset.yaml
```

Estos entry points se incorporarán cuando las fases experimentales correspondientes queden congeladas.

## Documentación

- [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md): niveles y criterios de reproducibilidad.
- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md): campos lógicos y validaciones requeridas.
- [`docs/USING_YOUR_OWN_DATA.md`](docs/USING_YOUR_OWN_DATA.md): flujo para replicar el protocolo con datos externos.
- [`docs/EXPERIMENT_PROTOCOL.md`](docs/EXPERIMENT_PROTOCOL.md): etapas canónicas del procedimiento experimental.
- [`docs/DATA_PROVENANCE.md`](docs/DATA_PROVENANCE.md): procedencia y redistribución de datos/corpus.
- [`docs/EXPECTED_RESULTS.md`](docs/EXPECTED_RESULTS.md): registro de resultados esperados para presets congelados.

## Datos y alcance

No se asumirán derechos de redistribución de datos administrativos o corpus normativos. Cada release debe documentar procedencia, transformaciones, versiones, hashes y mecanismo de reconstrucción cuando corresponda.

Para datos externos, el usuario es responsable de contar con autorización para procesarlos y de respetar sus restricciones de confidencialidad y redistribución.

La salida del framework es experimental y de apoyo a evaluación. No constituye una clasificación aduanera oficial ni sustituye revisión experta.

## Pendiente antes de la primera release estable

- cerrar la campaña experimental de referencia;
- congelar configuraciones canónicas;
- implementar el contrato de configuración;
- implementar validación de datasets externos;
- fijar entorno y dependencias exactas;
- incorporar un dataset sintético de prueba;
- implementar runners canónicos;
- implementar pruebas de reproducción y replicación;
- registrar hashes y procedencia de artefactos de referencia;
- definir `CITATION.cff` y licencia(s);
- ejecutar validación completa desde un entorno limpio.
