# gci-nandina-rag-reproducibility

Paquete de reproducibilidad asociado al proyecto de investigación [`gci-nandina-rag`](https://github.com/elVladdi/gci-nandina-rag).

## Propósito

Este repositorio se utilizará para publicar un **snapshot científico mínimo, versionado y verificable** de los experimentos finales de la tesis sobre recomendación auditable de subpartidas NANDINA mediante recuperación histórica, evidencia normativa y LLM local.

El repositorio principal continúa siendo el espacio de desarrollo, experimentación e historial metodológico. Este repositorio **no es un segundo entorno de desarrollo paralelo**: se poblará progresivamente con los artefactos necesarios para reproducir los resultados finales una vez que cada fase experimental haya sido cerrada y aprobada.

## Estado actual

**Scaffold inicial de reproducibilidad.**

Aún no se han incorporado los datasets, configuraciones finales ni resultados definitivos de EXP-04. La campaña experimental vigente continúa en el repositorio principal.

Estado conocido al crear este scaffold:

- split experimental vigente: `data_aduanas` Clase 87 `v0.2`;
- estrategia de partición: `T5-safe-159`;
- unidad de análisis: `SERIE`;
- unidad de agrupamiento experimental: `DECLARACION` / DAM;
- EXP-01 / EXP-02 / EXP-03: cerradas;
- Gate 5: cerrado;
- EXP-04 Fase A — BM25 histórico v0.2: completada;
- EXP-04 Fase B y posteriores: pendientes.

La evidencia y el código en desarrollo permanecen en:

- https://github.com/elVladdi/gci-nandina-rag

## Principios de este repositorio

1. **Snapshot, no desarrollo paralelo.** Solo se copiarán configuraciones, scripts canónicos y artefactos finales necesarios para reproducir la tesis.
2. **Versionado explícito.** Cada release deberá corresponder a un estado científico identificable del repositorio principal.
3. **Hashes verificables.** Datasets y artefactos críticos deberán registrar SHA-256.
4. **Sin sobrescritura silenciosa.** Una versión publicada no deberá reutilizar nombres para contenidos diferentes.
5. **Ejecución determinística cuando sea posible.** Semillas, configuración, entorno y comandos deberán quedar documentados.
6. **Separación entre evidencia y desarrollo.** Resultados exploratorios, ramas diagnósticas y outputs intermedios permanecerán en el repositorio principal.
7. **Trazabilidad cruzada.** Cada release deberá indicar el commit fuente de `gci-nandina-rag` del que fue derivada.

## Estructura prevista

```text
.
|-- README.md
|-- .gitattributes
|-- .gitignore
|-- configs/               # configuraciones finales congeladas
|-- data/                  # datos publicables o instrucciones de obtención/verificación
|-- docs/
|   |-- REPRODUCIBILITY.md
|   |-- DATA_PROVENANCE.md
|   `-- EXPECTED_RESULTS.md
|-- scripts/               # runners canónicos de reproducción
|-- results/               # tablas, métricas, figuras y manifests finales
`-- tests/                 # verificaciones de integridad y regresión científica
```

La estructura puede ampliarse cuando finalice EXP-04, pero debe mantenerse deliberadamente más pequeña que el repositorio principal.

## Flujo previsto de publicación

```text
gci-nandina-rag
      |
      | cierre de experimento + Gate aprobado
      v
commit científico congelado
      |
      | extracción de artefactos necesarios
      v
gci-nandina-rag-reproducibility
      |
      | validación desde entorno limpio
      v
release reproducible de tesis
```

## Reproducción final prevista

El objetivo es que una versión final permita ejecutar un comando equivalente a:

```bash
python scripts/reproduce_all.py
```

y verificar automáticamente datasets, configuraciones, métricas, tablas y figuras contra resultados esperados.

Ese comando **todavía no existe** porque la campaña experimental no ha concluido.

## Datos

No se asumirán automáticamente derechos de redistribución de datos administrativos o corpus normativos. `docs/DATA_PROVENANCE.md` documentará para cada artefacto:

- procedencia;
- transformación;
- versión;
- hash;
- condiciones de redistribución;
- mecanismo alternativo de reconstrucción cuando el archivo no pueda publicarse.

## Relación con la tesis

Este repositorio será la referencia de reproducibilidad de la versión final de la tesis. No sustituye al proyecto de investigación aprobado ni al repositorio de desarrollo; su función es permitir reconstruir y verificar la evidencia computacional reportada.

## Pendiente antes de la primera release

- cerrar EXP-04;
- congelar configuraciones finales;
- fijar entorno y dependencias exactas;
- seleccionar artefactos publicables;
- registrar hashes y procedencia;
- implementar runners canónicos;
- implementar pruebas de reproducción;
- generar `CITATION.cff` con autoría validada;
- definir licencia(s) compatibles con código, datos y documentación;
- realizar una reproducción completa desde un entorno limpio.
