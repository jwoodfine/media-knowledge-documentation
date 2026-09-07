---
schema: foundry-doc-v1
title: "Cerrar un proyecto de construcción"
slug: close-out-a-construction-project
short_description: "Ejecuta los dos binarios de reporte de cierre de obra — un marcador operativo ponderado de seis categorías y una lista de verificación y paquete de revisión de cierre de obra — cualquiera de los cuales también puede ejecutarse como una comprobación de estado en vivo sobre un proyecto todavía en construcción."
category: how-to
index_group: financial-construction-tools
content_type: how-to
type: how-to
quality: complete
status: active
audience: "Engineers (hands on keyboard); customer operators"
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: close-out-a-construction-project.md
research_trail:
  sources: [tool-construction-tco-26/src/bin/ksf_evidence.rs, tool-construction-tco-26/src/bin/job_closeout.rs, tool-construction-tco-26/src/compute/ksf_evidence.rs, tool-construction-tco-26/src/compute/job_closeout.rs]
  verification_method: "traducción directa del borrador en inglés, verificado y revisado"
---

## Requisitos previos

- Todo lo indicado en los requisitos previos de [[monitor-an-active-construction-project]].
- Los reportes de seguimiento continuo (particularmente costo a completar y exposición por órdenes de cambio) ya generados al menos una vez — el paquete de cierre de obra cita sus cifras directamente en lugar de recalcularlas.

## Propósito

Producir los dos reportes que un gerente de proyecto o líder de operaciones usa cuando un proyecto se acerca a su finalización: un marcador operativo ponderado en seis categorías, y una lista de verificación y paquete de revisión de cierre de obra. Ninguno de los dos reportes requiere que el proyecto esté realmente terminado para ejecutarse — ambos están diseñados para usarse como una comprobación de estado en vivo a lo largo de todo el proyecto, y ambos lo indican con honestidad si el proyecto todavía está en construcción.

## Procedimiento

### 1. Apunte ambos binarios al directorio de datos del proyecto

```bash
export TCO26_DATA_DIR=/data/construction/example-project
```

### 2. Ejecute el marcador operativo

```bash
cargo run --bin ksf_evidence -p tool-construction-tco-26
```

Esto renderiza un marcador ponderado completo — seis categorías (seguridad, calidad, relaciones con el cliente, control de costos, cronograma y documentación), cada una con su propio conjunto de preguntas reales y ponderaciones por puntos. Un pequeño número de preguntas en la categoría de seguridad se evidencian automáticamente a partir del registro de actividad de seguridad cubierto en [[monitor-an-active-construction-project]] — por ejemplo, si existe un envío mensual requerido en el registro para un período dado es un hecho que el registro puede confirmar mecánicamente. Cada otra pregunta — que es la mayoría — está marcada explícitamente como que requiere revisión humana. Ninguna pregunta se califica automáticamente jamás con base en un juicio que este motor no tiene manera de observar.

### 3. Ejecute el paquete de cierre de obra

```bash
cargo run --bin job_closeout -p tool-construction-tco-26
```

Esto renderiza una lista de verificación completa de cierre de obra (docenas de elementos reales de cierre — inspecciones finales, documentación de garantía, entrega de llaves, y similares) y una plantilla estructurada de revisión del proyecto. Un pequeño número de elementos de la lista de verificación que corresponden a cifras que el motor ya calcula — una proyección final de costos, un resumen de la exposición por órdenes de cambio — citan esas cifras directamente de los reportes cubiertos en [[monitor-an-active-construction-project]], cada una etiquetada explícitamente como una instantánea actual y en curso, no como una cifra final. Cada otro elemento es una plantilla de llenado simple, ya que la mayoría de las acciones de cierre (devolver llaves, cancelar seguros, obtener una confirmación de transferencia de servicios públicos) son acciones del mundo físico que este motor no tiene manera de observar.

## Resultado esperado

Dos archivos cada uno, `ksf_evidence.{html,pdf}` y `job_closeout.{html,pdf}`, escritos en `<data-dir>/outputs/<year>/`.

## Verificación

- **Ninguno de los dos reportes afirma jamás por su propia autoridad que el proyecto está completo.** Si ejecuta estos reportes contra un proyecto que todavía está en construcción, ambos lo indican con claridad — el paquete de cierre de obra en particular establece de forma directa, en su propia nota de base de preparación, que está renderizando contra un proyecto en curso. Si un reporte que genera parece estar afirmando finalización, eso es un defecto que vale la pena reportar, no un resultado esperado.
- **Abra ambos PDF y confirme que cada cifra citada está etiquetada como una instantánea, no como una cifra final**, dondequiera que un elemento de la lista de verificación o una pregunta del marcador cite datos de otro reporte.
- **Vuelva a ejecutar el panel de control de [[monitor-an-active-construction-project]]** después para confirmar que ambos archivos nuevos aparecen bajo la sección "Job Completion."

## Lo que esta tarea no hace

- **No califica preguntas de juicio.** La gran mayoría de las preguntas del marcador, y varios elementos de la lista de verificación de cierre, requieren la propia evaluación de un revisor humano. Nada en ninguno de los dos reportes intenta responderlas.
- **No calcula una cifra final de costo ni de órdenes de cambio.** Ambos reportes citan las cifras actuales y en curso de los reportes de seguimiento continuo — ejecutar esta tarea no cierra por sí misma los libros de un proyecto.
- **No requiere que el proyecto esté terminado.** Ambos binarios se ejecutan contra el estado de cualquier proyecto, en cualquier momento — no existe una condición de error de "el proyecto aún no está terminado."

## Casos especiales

- **Un proyecto sin datos reales detrás de los reportes que cita, aun así, renderiza un reporte completo** — cada elemento de la lista de verificación y cada pregunta del marcador está presente, con los campos de citación mostrando un estado honesto de "no medido" en lugar de omitirse.

## Reversión (rollback)

Nada que revertir — ambos binarios leen archivos existentes y solo escriben en `<data-dir>/outputs/<year>/`.

## Próximos pasos

- [[monitor-an-active-construction-project]] — los reportes que citan estos reportes de cierre de obra

## Ver también

- [[tool-construction]] — el diseño del ledger y la lista completa de reportes, incluidos estos dos
