---
schema: foundry-doc-v1
title: "La familia de herramientas financieras y de construcción — un diseño compartido en tres productos"
slug: financial-and-construction-tools-overview
category: applications
type: tool
content_type: topic
quality: complete
index_group: financial-and-construction-tools
status: active
audience: vendor-public
bcsc_class: forward-looking
language_protocol: PROSE-TOPIC
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: financial-and-construction-tools-overview.md
short_description: "Cómo se relacionan tool-accounting, tool-construction y tool-payroll como una sola familia de productos — un diseño compartido de partida doble, alimentaciones de datos unidireccionales entre ellos y un límite compartido de arquitectura gratuita/pagada."
cites: []
---

[[tool-accounting]], [[tool-construction]] y [[tool-payroll]] son tres productos separados que comparten un mismo linaje de diseño, no tres herramientas independientes que simplemente resultan estar cerca entre sí. Este artículo cubre lo que comparten y cómo se conectan; cada artículo de herramienta cubre su propio dominio en profundidad.

## Un diseño de partida doble, tres dominios

Las tres herramientas están construidas sobre, o diseñadas en torno a, la misma disciplina de libro contable de partida doble: cada asiento es una entrada balanceada, nada se almacena si puede derivarse de lo que ya está registrado, y se mantiene un historial completo e inalterable de cada entrada en lugar de sobrescribirla. `tool-accounting` aplica esta disciplina a los estados financieros. `tool-construction` aplica la misma disciplina a las cantidades físicas de construcción junto con los dólares, en un diseño de dos libros contables (un libro de producción para cantidades, un libro de costos para dólares) construido específicamente porque el seguimiento de costos de construcción necesita ambos a la vez. `tool-payroll` está diseñado para aplicar la misma disciplina subyacente al cálculo de pago bruto a neto y a la temporización de las remesas estatutarias.

**Por qué importa:** un diseño compartido significa que una corrección o mejora en la mecánica subyacente del libro contable está pensada para beneficiar a las tres herramientas, no solo a una, y un desarrollador o auditor que entiende el modelo contable de una herramienta ya entiende la forma de las otras dos.

## Cómo se mueven los datos entre ellas — solo alimentaciones unidireccionales

Las tres herramientas están diseñadas para conectarse mediante puentes de datos unidireccionales, nunca una tabla compartida y nunca un valor que se convierte de vuelta a su origen:

- **`tool-construction` → `tool-accounting`**: costo en dólares, que alimenta los estados financieros del propietario como obra en construcción en proceso.
- **`tool-construction` → `tool-payroll`**: horas y clase de mano de obra, que alimentan la nómina como tarjetas de tiempo. Este puente está diseñado para funcionar en un solo sentido — los dólares regresan únicamente como asientos ordinarios de nómina y cuentas por pagar hacia el libro de costos de construcción, a través de la misma ruta de revisión que cualquier otra transacción de origen, nunca como una conversión automática de horas mediante una tarifa. Esa brecha entre una estimación de horas por tarifa y los dólares reales de nómina es una característica de diseño deliberada, no una omisión: es la variación de tarifa de mano de obra, y cerrar el ciclo automáticamente destruiría precisamente la señal que existe para revelar.

**Por qué importa:** un propietario o auditor que evalúa estas herramientas en conjunto no necesita conciliar los números entre ellas manualmente — el diseño unidireccional significa que el propio libro contable de cada herramienta sigue siendo la fuente autorizada de su propio dominio, y cada otra herramienta lo recibe como una entrada fechada, nunca como un valor mutable compartido.

## Límite de arquitectura gratuita/pagada compartido

Las tres herramientas se apoyan en la misma arquitectura de plataforma subyacente: el sustrato de archivo y la terminal que las aloja son gratuitos (Apache-2.0); la agregación entre archivos — la única capacidad que un archivo aislado genuinamente no puede realizar por sí mismo — es el límite pagado en toda la plataforma. Cada una de las tres herramientas está diseñada como su propia superficie comercial adicional y separada sobre ese límite compartido, vendiendo la ingeniería de dominio en sí (el motor contable, la mecánica del libro contable de construcción, el motor de cálculo de nómina) en lugar de un margen sobre una infraestructura que ya es gratuita de operar.

## Licenciamiento

`tool-accounting`, `tool-construction` y `tool-payroll` están licenciados bajo
AGPL-3.0-or-later. Existe una licencia PointSav-Commercial independiente como alternativa
de pago para quien necesite distribuir una versión modificada, u ofrecerla como servicio
de red, sin la obligación de copyleft.

## Estado de construcción, lado a lado

| Herramienta | Estado real hoy |
|---|---|
| `tool-accounting` | La más avanzada en su cadena de herramientas original: código real, construido y ejecutado contra datos históricos reales de principio a fin para la producción de estados financieros, con la consolidación ya integrada. Una segunda cadena de herramientas piloto, para la industria de la construcción, construida sobre la misma biblioteca central, produce reportes de cuaderno de disposiciones y cumplimiento estatutario para una obra activa. |
| `tool-construction` | Código real, en ejecución contra un piloto activo: tanto el ledger completo del lado de cantidades como un ledger de costos genuinamente denominado en dinero están construidos, impulsando más de una docena de reportes reales a través de cadencias de arranque, seguimiento continuo y cierre de obra. Los datos de estimación y cronograma son reales; los datos de costo real, seguridad y cierre de obra están estructuralmente listos pero aún no poblados, porque el piloto todavía no ha llegado al punto en que esos datos existan. |
| `tool-payroll` | Un reporte real construido y en funcionamiento — un registro de nómina a nivel de división que agrega horas de mano de obra presupuestadas bajo las reglas de tiempo salarial citadas de una jurisdicción. El cálculo de pago bruto a neto, la frecuencia de pago y el cálculo de remesas siguen siendo solo de diseño. |

**Por qué importa:** las tres herramientas se discuten frecuentemente juntas por su diseño
compartido, pero no están en la misma etapa de madurez — lea el artículo propio de cada
herramienta para el detalle detrás de este resumen antes de tratar a cualquiera de las
tres como un producto terminado.

## Consolidación multi-edificio y multi-proyecto

Dos situaciones genuinamente distintas se llaman ambas "consolidación," y la plataforma las trata de forma diferente a propósito.

**Varios edificios bajo una sola entidad legal** — una estructura inmobiliaria común, donde una entidad tiene el título de propiedad de más de un edificio — comparten el motor contable único de esa entidad, ya que las declaraciones estatutarias, los estados financieros y el reporte del cuaderno de disposiciones son obligaciones de la entidad, no de ningún edificio en particular. Pero cada edificio mantiene su propio motor de construcción, porque dos edificios en el mismo sitio pueden tener combinaciones de oficios, códigos de costo y cronogramas completamente diferentes aunque respondan a los mismos libros. `tool-construction` mismo produce la vista consolidada a través de los edificios de una entidad; ver su propio artículo para más detalle.

**Un contratista o administrador de propiedades que opera varios desarrollos separados y legalmente distintos a la vez** es un caso completamente diferente, y no es algo que ninguna herramienta individual de esta familia construya por sí misma. Consultar o comparar datos entre archivos genuinamente separados — "qué socio comercial tiene una tasa de defectos en aumento en todo nuestro portafolio," "cuál de nuestros proyectos está atrasado respecto a los demás" — es una capacidad de toda la plataforma, vendida por separado de cualquier motor de dominio. Existen hoy dos componentes reales en este espacio, en etapas distintas: `app-orchestration-bim`, que realiza este tipo de consolidación para datos de modelos de información de construcción (BIM) entre propiedades, está construido pero aún no desplegado; una capa de consolidación comparable para los dominios contable y de construcción, referida bajo el nombre de trabajo `app-orchestration-accounting`, es solo un nombre y alcance propuestos — nada bajo ese nombre se ha construido todavía.

**Por qué importa:** un propietario o inversionista que evalúa un edificio, o las propiedades de una sola entidad legal, obtiene esa vista directamente de las herramientas de esta familia. Un operador que gestiona muchos desarrollos separados a la vez debería esperar que la vista más completa entre portafolios provenga de un producto separado a nivel de plataforma, no de que un motor de dominio individual desarrolle esa capacidad internamente.

## Véase también

- [[tool-accounting]]
- [[tool-construction]]
- [[tool-payroll]]
- [[legal-and-ip-structure]] — la justificación completa de los niveles de licenciamiento corporativo que resume la sección de Licenciamiento de este artículo
