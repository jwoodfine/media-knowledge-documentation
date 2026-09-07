---
schema: foundry-doc-v1
title: "Monitorear un proyecto de construcción activo"
slug: monitor-an-active-construction-project
short_description: "Ejecuta ocho binarios de reporte independientes de cadencia recurrente — estado, excepciones, costo a completar, exposición por órdenes de cambio, seguimiento de subcontratos y equipo, actividad de seguridad, y consolidación de portafolio — contra el directorio de datos de un proyecto, más el panel de control que confirma qué quedó realmente en disco."
category: how-to
index_group: financial-construction-tools
content_type: how-to
type: how-to
quality: complete
status: active
audience: "Engineers (hands on keyboard); customer operators"
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: monitor-an-active-construction-project.md
research_trail:
  sources: [tool-construction-tco-26/src/bin/status_report.rs, tool-construction-tco-26/src/bin/compliance_exceptions.rs, tool-construction-tco-26/src/bin/cost_to_complete.rs, tool-construction-tco-26/src/bin/change_order_exposure.rs, tool-construction-tco-26/src/bin/subcontract_status.rs, tool-construction-tco-26/src/bin/equipment_utilisation.rs, tool-construction-tco-26/src/bin/safety_activity_summary.rs, tool-construction-tco-26/src/bin/portfolio_rollup.rs, tool-construction-tco-26/src/bin/report_index.rs]
  verification_method: "traducción directa del borrador en inglés, verificado y revisado; cada bloque de comandos se dejó idéntico byte a byte a la versión en inglés, ya que son comandos literales a ejecutar, no texto descriptivo"
---

## Requisitos previos

- Todo lo indicado en los requisitos previos de [[generate-a-construction-cost-estimate]] — un entorno de Rust funcional, una copia local (checkout) del espacio de trabajo, y `TCO26_DATA_DIR` apuntando al directorio de datos de un proyecto real.
- Los reportes de arranque del proyecto (estimación de costos, cronograma, paquetes de trabajo) ya generados al menos una vez — varios de los reportes a continuación leen los mismos archivos de origen.
- No es necesario tener ningún servicio en ejecución para ninguno de los ocho binarios de reporte a continuación. (Esto es distinto de los binarios `calibrate`/`solve_rate`/`post_ledger` de la canalización de arranque, que sí dependen de servicios residentes en el archivo — ver la sección de Servicios de plataforma de [[tool-construction]].)

## Propósito

Producir los reportes de cadencia recurrente que un gerente de proyecto, propietario o prestamista revisa de forma continua mientras un proyecto está en construcción: estado general, excepciones de actualidad de datos, pronóstico de costo a completar, exposición por órdenes de cambio, seguimiento de subcontratos y equipo, un registro de actividad de seguridad, y — cuando la entidad legal del proyecto posee más de un edificio — una consolidación entre ellos.

Cada uno de los ocho reportes a continuación es su propio binario independiente. No existe un comando único de "ejecutar todo"; ejecute los que sean relevantes para la revisión que corresponda.

## Procedimiento

### 1. Apunte cada binario al mismo directorio de datos

```bash
export TCO26_DATA_DIR=/data/construction/example-project
```

Cada binario de esta guía lee esta misma variable de entorno — la misma que usan los reportes de arranque. Nada aquí introduce una segunda variable con un nombre distinto.

### 2. Ejecute el o los reportes que necesite

```bash
cargo run --bin status_report -p tool-construction-tco-26
cargo run --bin compliance_exceptions -p tool-construction-tco-26
cargo run --bin cost_to_complete -p tool-construction-tco-26
cargo run --bin change_order_exposure -p tool-construction-tco-26
cargo run --bin subcontract_status -p tool-construction-tco-26
cargo run --bin equipment_utilisation -p tool-construction-tco-26
cargo run --bin safety_activity_summary -p tool-construction-tco-26
cargo run --bin portfolio_rollup -p tool-construction-tco-26
```

Cada uno es una invocación completa e independiente — ninguno recibe parámetros (flags), y ejecutar uno no requiere haber ejecutado ningún otro antes. Cada uno imprime una línea de resumen con conteos estructurales en stdout y escribe su propio HTML y PDF en `<data-dir>/outputs/<year>/`, siguiendo la misma convención de nombres que el propio binario de reporte (por ejemplo, `status_report` escribe `status_report.html`/`.pdf`).

### 3. Verifique lo que existe con el panel de control (dashboard)

```bash
cargo run --bin report_index -p tool-construction-tco-26
```

Esto renderiza una única página de navegación (`<data-dir>/outputs/<year>/gp26-report-index.html` en el despliegue de referencia — el propio nombre de archivo lleva un prefijo específico del despliegue, siguiendo la convención de nomenclatura propia del crate por despliegue) que enumera cada reporte que este motor puede producir, agrupado por la misma cadencia de arranque / mensual / cierre de obra usada en esta guía, con una columna de estado verificada en vivo: un reporte que nunca se ha generado se muestra como "Not yet generated" en lugar de un enlace roto. Ejecute esto después de generar cualquier subconjunto de reportes para confirmar qué quedó realmente en disco.

## Resultado esperado

| Reporte | Salida real |
|---|---|
| Reporte mensual de estado del proyecto | Cifras de presupuesto, cronograma y avance trazadas a entradas reales; cualquier dato no medido se renderiza como un guion largo |
| Registro de excepciones de actualidad de datos | Cada paquete de trabajo o fase del cronograma sin actualización de avance o real en este período |
| Pronóstico de costo a completar | Tres fórmulas estándar de la industria para la estimación al finalizar, cada una renderizando un guion largo en lugar de un número dondequiera que el costo real o el valor ganado que necesita no se haya medido aún |
| Registro de exposición por órdenes de cambio | Paquetes de trabajo sin margen de presupuesto no comprometido restante, y paquetes de trabajo sin ningún asiento en el ledger todavía |
| Compromiso / estado de certificación de subcontratos | Cifras reclamadas versus certificadas para cada paquete de trabajo subcontratado |
| Utilización / recuperación de equipo | Horas operadas versus inactivas y la variación de productividad resultante para cada paquete de trabajo codificado como equipo |
| Registro de actividad de seguridad | Conteos mensuales en las categorías que rastrea un programa real de seguridad de obra — inspecciones, charlas de seguridad, severidad de incidentes, horas trabajadas |
| Consolidación de portafolio | Una fila por cada edificio que posee esta entidad legal, más un total — una fila hoy en el despliegue de referencia, ya que actualmente tiene exactamente un edificio registrado |

## Verificación

- **Abra cada PDF y véalo**, la misma disciplina que [[generate-a-construction-cost-estimate]] describe para los reportes de arranque — una compilación exitosa no es la misma afirmación que una página legible.
- **Un reporte que muestra cada cifra como un guion largo, o una tabla con cero filas, no está necesariamente roto.** Varios de estos reportes están estructuralmente listos y completamente probados pero no tienen nada real que mostrar todavía en un proyecto que no ha alcanzado el hito correspondiente — un proyecto sin ningún envío de seguridad registrado este mes debería mostrar un registro de seguridad vacío, no uno fabricado. Lea la propia nota de "base de preparación" de cada reporte (impresa en el propio reporte) antes de tratar un resultado vacío como un defecto.
- **Vuelva a ejecutar el panel de control** después de generar un lote de reportes para confirmar que el conteo de reportes "presentes" coincide con lo esperado.

## Lo que esta tarea no hace

- **No califica automáticamente juicios humanos.** El marcador de cierre de obra cubierto en [[close-out-a-construction-project]] es el ejemplo más claro, pero la misma regla aplica aquí: ningún reporte de esta familia convierte jamás una pregunta de juicio humano en un sí/no automatizado.
- **No calcula una tasa de incidentes de seguridad.** El registro de actividad de seguridad reporta solo conteos reales; un cálculo de tasa (incidentes por horas trabajadas) depende de una convención de normalización específica de la jurisdicción que no se ha verificado de forma independiente, por lo que no se calcula ninguna.
- **Aún no es multi-proyecto entre desarrollos separados.** La consolidación de portafolio cubre varios edificios bajo una sola entidad legal — ver [[financial-and-construction-tools-overview]] para entender por qué ese es un caso diferente de un contratista que opera varios desarrollos separados a la vez.

## Casos especiales

- **Un reporte cuyo CSV subyacente tiene un encabezado que no coincide con el esquema esperado falla al cargar**, la misma convención de fallo explícito que sigue cada reporte de esta familia — nunca una lectura parcial silenciosa.
- **El total de la consolidación de portafolio se deja en blanco (no en cero) si la cifra de algún edificio no está medida** — un total solo se muestra cuando cada entrada del mismo es real, de modo que un edificio genuinamente no medido nunca desaparece silenciosamente del total como si hubiera contribuido con cero.

## Reversión (rollback)

Nada que revertir en los datos de origen — cada binario de esta guía lee archivos existentes y solo escribe en `<data-dir>/outputs/<year>/`. Elimine los archivos de salida, o vuelva a ejecutar para reemplazarlos.

## Próximos pasos

- [[close-out-a-construction-project]] — los reportes de cierre de obra a los que eventualmente conduce la cadencia de esta guía
- [[generate-a-construction-draw-workbook]] — los reportes hermanos del lado contable que cubren la misma exposición estatutaria y de plazo de pago del proyecto

## Ver también

- [[tool-construction]] — el diseño del ledger y la lista completa de reportes de la que provienen estos binarios
- [[generate-a-construction-cost-estimate]] — los reportes de arranque de los que leen varios de estos
