---
schema: foundry-doc-v1
title: "Generar un cuaderno de trabajo de disposiciones de construcción"
slug: generate-a-construction-draw-workbook
short_description: "Ejecuta los cinco binarios de reporte de la extensión de tool-accounting-tco-26 para la industria de la construcción — solicitud y cronograma de llamado de capital, declaración estatutaria, cheques emitidos, y calendario de flujo de caja — que cubren la exposición estatutaria y de plazos de pago de una obra activa."
category: how-to
index_group: financial-construction-tools
content_type: how-to
type: how-to
quality: complete
status: active
audience: "Engineers (hands on keyboard); customer operators"
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: generate-a-construction-draw-workbook.md
research_trail:
  sources: [tool-accounting-tco-26/src/bin/capital_call_request.rs, tool-accounting-tco-26/src/bin/capital_call_schedule.rs, tool-accounting-tco-26/src/bin/statutory_declaration.rs, tool-accounting-tco-26/src/bin/checks_issued.rs, tool-accounting-tco-26/src/bin/cash_flow_calendar.rs]
  verification_method: "traducción directa del borrador en inglés, verificado y revisado"
---

## Requisitos previos

- Un entorno de Rust funcional y una copia local (checkout) del espacio de trabajo que contiene los crates contables.
- Un directorio de datos con el registro compartido de entidad/cuenta/consolidación completo para la entidad contra la que se ejecutan estos reportes, más datos reales de bloqueo de presupuesto del motor de construcción hermano (ver [[tool-construction]]).
- No es necesario tener ningún servicio en ejecución para ninguno de los cinco binarios a continuación.

## Propósito

Producir el paquete de reportes que los directores independientes de un inversionista de capital, o un prestamista de construcción donde exista uno, revisan durante una obra activa: qué se está solicitando contra el presupuesto aprobado del proyecto, la declaración estatutaria exigida por la ley de gravámenes de construcción, un registro de los pagos efectivamente desembolsados, y un calendario de fechas de vencimiento de pago estatutarias en cascada a partir de fechas reales de recepción de factura.

Esta guía es distinta de [[generate-a-financial-statement-package]], que cubre una cadena de herramientas de estados financieros a nivel de entidad diferente — esta es específica al propio ciclo de disposiciones/llamados de capital de un proyecto de construcción activo.

## Procedimiento

### 1. Apunte cada binario al directorio de datos compartido

```bash
export TCO26_DATA_DIR=/data/construction/example-project
```

La misma variable de entorno que usan los reportes del lado de construcción — esta cadena de herramientas lee del mismo directorio de datos, no de uno separado.

### 2. Ejecute los reportes

```bash
cargo run --bin capital_call_request -p tool-accounting-tco-26
cargo run --bin capital_call_schedule -p tool-accounting-tco-26
cargo run --bin statutory_declaration -p tool-accounting-tco-26
cargo run --bin checks_issued -p tool-accounting-tco-26
cargo run --bin cash_flow_calendar -p tool-accounting-tco-26
```

Cada uno es independiente — ejecutar uno no requiere haber ejecutado ningún otro antes, y cada uno escribe su propio HTML y PDF en `<data-dir>/outputs/<year>/`.

## Resultado esperado

| Reporte | Salida real |
|---|---|
| Solicitud de llamado de capital | La solicitud actual a los directores independientes de la entidad, dimensionada contra el presupuesto aprobado real |
| Cronograma de llamado de capital | El registro corriente de Estimación Original / Estimación Revisada / Costos Completados a la Fecha / Costo por Completar / % Completado / Retención del que se nutre un llamado de capital |
| Declaración estatutaria | La atestación jurada de cumplimiento de gravamen/retención que exige el estatuto de gravámenes de construcción subyacente |
| Cheques emitidos | Un registro de desembolsos vinculado a la cuenta de efectivo real — genuinamente vacío hasta que se haya desembolsado un pago real |
| Calendario de flujo de caja | Un calendario de fechas de vencimiento de pago del propietario y del subcontratista, cada una en cascada a partir de una fecha real de recepción de factura |

## Verificación

- **Abra cada PDF y véalo.**
- **Un registro genuinamente vacío no es un defecto.** En un proyecto sin ninguna nómina, factura o pago registrado jamás, los cinco reportes correctamente se renderizan con saldos reales y honestos en cero o una tabla vacía — no con una cifra fabricada. Lea la propia nota de base de preparación de cada reporte antes de tratar un resultado vacío como algo roto.
- **Confirme que los reportes de llamado de capital nunca usan lenguaje de prestamista** (una "solicitud de disposición" a un banco) si la entidad contra la que está ejecutando esto se financia con capital propio — el propio planteamiento del reporte sigue la estructura de financiamiento real de la entidad en lugar de recurrir por defecto a la terminología de préstamos.

## Lo que esta tarea no hace

- **No calcula la cascada de fechas de vencimiento estatutarias a partir de nada que no sea una fecha real y registrada de recepción de factura.** Una fecha de recepción de factura en blanco no produce ninguna entrada de calendario para esa transacción, no una estimada.
- **No desembolsa ni autoriza un pago.** Estos reportes leen el ledger; nada en esta tarea escribe un pago o un cheque.

## Casos especiales

- **Un proyecto sin transacciones reales registradas todavía** produce un conjunto completo y real de cinco reportes, todos mostrando correctamente saldos vacíos — este es el estado esperado para un proyecto en su arranque, no un error.

## Reversión (rollback)

Nada que revertir — cada binario lee archivos existentes y solo escribe en `<data-dir>/outputs/<year>/`.

## Próximos pasos

- [[monitor-an-active-construction-project]] — los reportes del lado de construcción a los que finalmente se remontan las cifras de este cuaderno de trabajo

## Ver también

- [[tool-accounting]] — la sección sobre la extensión para la industria de la construcción que describe el diseño de esta cadena de herramientas
- [[tool-construction]] — el modelo de retención y garantía del que depende la declaración estatutaria y el calendario de flujo de caja de este cuaderno de trabajo
- [[generate-a-financial-statement-package]] — la cadena de herramientas de estados financieros a nivel de entidad de la que esta guía es distinta
