---
schema: foundry-doc-v1
title: "tool-construction — libro contable de costo, cronograma y calidad para construcción"
slug: tool-construction
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
paired_with: tool-construction.md
short_description: "Libro contable de archivos planos, bajo control del propietario, para costo, cronograma y control de calidad de construcción, sobre la misma disciplina de partida doble que tool-accounting; tanto el ledger de cantidades como el de dinero ya funcionan como CLI real contra un piloto en vivo, renderizando más de una docena de reportes en tres cadencias — sin consola todavía."
cites: []
---

`tool-construction` es un libro de contabilidad (ledger) en archivos planos, de propiedad del operador, para el control de costos, cronograma y calidad en construcción, construido sobre la misma disciplina de partida doble que el motor contable hermano, [[tool-accounting]]. Está diseñado para servir a tres audiencias a la vez: una referencia de implementación para desarrolladores que construyen la plataforma (incluyendo desarrolladores sin experiencia en construcción), una visión técnica para evaluar el negocio que el software respalda, y un documento de decisión para un contratista o propietario que evalúa su adopción.

**Lo que existe hoy.** El motor es real y está en funcionamiento, en ambos lados de su ledger. `tool-construction-core` implementa por completo el ledger del lado de cantidades — asientos de diario con valores vectoriales, unidades no fungibles, y las cuatro cadenas de tipo de costo (mano de obra, material, equipo, subcontrato) — y ahora también implementa un ledger de costos genuinamente denominado en dinero (ver más abajo), ambos verificados mediante pruebas de valores dorados (golden-value tests) que reproducen los ejemplos trabajados de la propia arquitectura, cifra por cifra. Un crate binario piloto opera todo el motor como una cadena de herramientas de línea de comandos con binarios de reporte y de canalización (pipeline) que construyen una estimación de costos de abajo hacia arriba a partir de paquetes de trabajo, calculan un cronograma de ruta crítica, registran la estimación en el ledger, y renderizan más de una docena de reportes reales en HTML y PDF. Funciona contra un piloto real: un proyecto de desarrollo activo cuyos paquetes de trabajo se registran como bloqueos de presupuesto reales, sin fallas de identidad de balance de comprobación. Un límite sigue siendo real: la cadena de herramientas es solo de línea de comandos — no existe ninguna terminal ni superficie de consola, y no se le ha asignado ningún espacio de compilación (build slot).

---

## El problema que resuelve

El control de costos en construcción divide un proyecto en un **código de costo** — una estructura de desglose de trabajo numerada que identifica un tipo de trabajo en un proyecto, como concreto colado in situ o montaje de acero estructural. Cada costo se divide además por **tipo de costo** — mano de obra, materiales, equipo y subcontrato — porque los cuatro se comportan de manera lo suficientemente distinta como para que combinarlos destruya la información que una desviación de costo (overrun) de otro modo revelaría.

Debajo del código de costo está el **paquete de trabajo**: una pieza definida de trabajo físico que lleva dos números cuya relación es la base del diseño — una **cantidad** (obtenida midiendo los planos) y un **factor unitario** (las horas de mano de obra para instalar una unidad de esa cantidad). La cantidad establece el presupuesto de mano de obra; la cantidad realmente instalada después consume ese presupuesto. Las horas trabajadas nunca consumen nada por sí solas — son aquello contra lo cual se mide el consumo.

**Por qué importa:** invertir esta dirección es uno de los errores reales más comunes en el costeo de obras, y es la falla específica que el motor previene estructuralmente, no por convención.

---

## Gestión del Valor Ganado (Earned Value Management) y costeo por reflujo (backflush)

Gastar y avanzar no son lo mismo — un proyecto que ha gastado el 60% de su presupuesto puede estar 30% u 80% completo. El motor usa **Gestión del Valor Ganado**, siguiendo el Valor Planificado, el Valor Ganado y el Costo Real como tres números en la misma unidad, de modo que la eficiencia de costos y el cumplimiento del cronograma se convierten en indicadores adelantados en lugar de algo visible solo después de los hechos. El diseño se protege específicamente contra un modo de falla conocido: si el valor ganado se derivara de las horas gastadas en lugar de la cantidad instalada observada de forma independiente, la medición compararía una cantidad consigo misma y nunca podría reportar un problema — por lo que la cantidad instalada reportada de forma independiente se trata como obligatoria, no opcional. Las identidades del valor ganado se aplican como pruebas unitarias de balance de comprobación sobre el pliegue (fold) del ledger, no calculadas mediante una fórmula separada que pudiera desviarse de los asientos.

El consumo de material se construye alrededor del **costeo por reflujo (backflush)**: en lugar de rastrear cada movimiento físico de material en obra, el sistema registra lo que se produjo y calcula hacia atrás lo que debió consumirse, usando un factor conocido, dejando solo la diferencia contra un conteo físico periódico para investigar. Esto se impulsa por la cantidad instalada, nunca por las horas trabajadas — impulsarlo por horas permitiría que una cuadrilla lenta parezca haber consumido más material del que un muro realmente contiene, confundiendo una señal de mano de obra con una señal de material.

**Por qué importa:** ambos mecanismos convierten el reporte diario de campo en una alerta temprana sobre problemas de costo y cronograma, meses antes de que un estado financiero mostraría lo mismo. En el piloto actual, esta maquinaria está construida y probada pero aún no alimentada: no existe ninguna fuente independiente de cantidad instalada para el proyecto, por lo que las columnas de valor ganado no reportan nada en lugar de una suposición derivada — ver la regla de reporte más abajo.

---

## Un solo diario, dos ledgers, ambos reales

El motor mantiene dos ledgers, denominados de manera diferente a propósito — y ambos ya están construidos, no solo diseñados. El **ledger de producción** contiene cantidades físicas — horas, metros cúbicos, toneladas, fracciones de la lista de valores del cronograma (schedule of values). El **ledger de costos** contiene dólares, alimentado únicamente por asientos reales de nómina y cuentas por pagar, nunca convirtiendo las cantidades del ledger de producción mediante una tarifa. La obligación de un subcontratista de suma alzada es una función del contrato y el porcentaje certificado como completo, no de las horas que trabajó su propia cuadrilla — dos ledgers mantienen ese caso honesto, en lugar de forzar una cifra de dólares inventada o una aproximación basada en horas.

El ledger de costos usa su propio plan de cuentas, numerado deliberadamente para que nunca pueda colisionar con el del ledger de cantidades — cuentas de costo directo por tipo de costo, nómina devengada, cuentas por pagar comerciales, y una cuenta dedicada de retención por pagar a subcontratistas que rastrea la retención estatutaria por contrato. En el piloto actual, cada saldo del ledger de costos es un cero real, calculado: no ha entrado ninguna factura, pago o registro de nómina a la canalización todavía, por lo que el ledger correctamente reporta nada en lugar de una estimación. Un cero real proveniente de un ledger vacío y una cifra genuinamente no medida se tratan como hechos diferentes en todo este motor — ver la regla de reporte más abajo.

Los dos ledgers son proyecciones nombradas de un solo diario, no libros separados — un enfoque con precedente en producción (el propio Material Ledger de SAP fue eventualmente integrado en un único Universal Journal). Dentro del ledger de cantidades específicamente, distintos tipos de cantidad nunca se suman entre sí. El tipo de unidad convierte esto en una garantía de compilación y ejecución, no en una convención:

```rust
pub enum Unit {
    Labour(LabourClass),
    Material(MaterialSpec),
    Equipment(EquipmentSpec),
    Contract(ContractUnit),
}
```

Un asiento de diario en el ledger de producción tiene valor vectorial — un evento del mundo real, como un día de trabajo reportado, se registra a través de varias unidades a la vez en un solo asiento atómico — y la verificación de balance se ejecuta componente por componente, exigiendo que los débitos y créditos de cada unidad balanceen de forma independiente. Sumar entre unidades se rechaza, no simplemente se desalienta. El estado de ningún ledger se almacena jamás como un total acumulado: cada ejecución vuelve a plegar (fold) el diario completo en los marcadores de cuenta, de modo que las identidades del balance de comprobación se vuelven a demostrar desde los primeros asientos en cada ejecución.

**Por qué importa:** los ledgers no pueden mezclar silenciosamente horas con metros cúbicos, ninguno de los dos con dólares, ni los dólares de un proyecto con los de otro, y cualquier corrupción del estado fallaría ruidosamente en el siguiente pliegue en lugar de acumularse en silencio.

---

## Las cuatro cadenas de tipo de costo

Cada tipo de costo tiene su propia cadena de cuentas y sus propias reglas de registro, porque cada una falla de manera distinta. Las cadenas de mano de obra y material implementan los mecanismos de bloqueo de presupuesto y consumo descritos arriba, incluyendo la precondición de disparo que le da fuerza al ledger: las cuentas de presupuesto rechazan de forma estricta un alivio excesivo hasta que una orden de cambio las vuelva a comprometer, mientras que las cuentas de almacén (stores) renderizan y marcan en lugar de bloquear — la distinción entre "este asiento sobregiraría una autorización" y "este asiento revela una variación que vale la pena investigar."

La **cadena de equipo** añade una dimensión de estado operativo a cada asiento — operado, inactivo (idle), en espera (standby), en tránsito — siguiendo la práctica establecida de la industria en costeo de obras en lugar de una taxonomía inventada. La pérdida de utilización y la variación de productividad se calculan entonces como filtros puros sobre el mismo pliegue del ledger, nunca como una fórmula derivada por separado, de modo que los dos componentes están garantizados estructuralmente a sumar exactamente el residual de la cadena, sin necesitar una verificación de conciliación.

La **cadena de subcontrato** modela una línea de la lista de valores del cronograma como una unidad de fracción de suma alzada y lleva la certificación y los mecanismos de retención a través del ledger. También impone la única regla que es genuinamente distinta de cualquier otra cadena: el residual del subcontrato debe cerrar en **exactamente cero**. Mientras que la variación de cualquier otra cadena es un número calculado que hay que explicar, un cierre de subcontrato se rechaza ante cualquier residual distinto de cero hasta que se registre una cancelación (write-off) explícitamente autorizada.

**Por qué importa:** una certificación de subcontrato sobre-reclamada, una máquina inactiva facturada como productiva, o un trabajo que continúa más allá de un presupuesto de orden de cambio agotado, cada uno aparece como un asiento rechazado o marcado en el momento del registro — no como una anomalía que alguien podría notar en un reporte semanas después.

---

## De quién es el ledger

Un ledger de costos de obra solo tiene sentido desde un asiento: la parte que efectivamente realiza el trabajo es la única parte que puede observar las horas de mano de obra, el consumo de material y los hechos de certificación. El motor hace esto explícito con un pequeño conjunto de roles de parte — ejecutor (performer), propietario contratante, certificador, subcontratista, proveedor — con exactamente un ejecutor por despliegue, cuyos libros son el ledger. La identidad de la parte se adjunta a un asiento solo donde el significado del asiento depende genuinamente de quién lo afirmó (la cadena de certificación de subcontrato, y el propio seguimiento de retención del ledger de dinero, que se mantiene por relación contractual); los asientos de mano de obra, material y equipo son hechos sobre el trabajo, no sobre una relación, y no llevan ninguna parte asociada. Una prueba de regresión ejecuta el mismo ciclo de vida de subcontrato dos veces, una con nombres reales de partes y otra con identificadores opacos, y verifica saldos idénticos — prueba mecánica de que la aritmética del ledger es independiente de quiénes son las partes.

En el piloto actual, el ejecutor es MCorp — el cliente de referencia de la plataforma, cuyo personal realiza el trabajo de construcción y opera el ledger piloto. El programa de desarrollo al que pertenece el proyecto es de Woodfine; el ledger modela los libros de la parte ejecutora, no los del propietario.

**Por qué importa:** los mismos cinco roles describen a un contratista general sin ninguna estructura de tenencia tan bien como describen al piloto — la prueba concreta de que el motor es software de dominio genérico, no la herramienta interna de una empresa con los nombres borrados.

---

## Reportes, y la regla de que un cero es una afirmación

La cadena de herramientas piloto renderiza más de una docena de reportes reales en HTML y PDF a través de `tool-typeset`, el renderizador de documentos de la plataforma, compartido y sin dependencias, a partir de una única capa de cálculo por reporte. Cada PDF renderizado se verifica visualmente, no solo por el éxito de la compilación — una disciplina adoptada después de que compilaciones exitosas produjeran un cronograma ilegible. Los reportes se dividen en tres cadencias, que corresponden a cómo realmente se revisa un proyecto real:

**Arranque (kick-off)**, producido una vez al inicio del proyecto: una estimación de costos de abajo hacia arriba, un cronograma de ruta crítica con línea de tiempo Gantt, la lista de materiales/paquetes de trabajo, y un registro de correspondencia que cita cada transmisión registrada mediante hash de contenido en lugar de copiar el contenido del mensaje en los propios registros del motor.

**Seguimiento continuo del proyecto**, producido en una cadencia recurrente: un reporte mensual de estado del proyecto; un registro de excepciones de actualidad de datos que marca cada paquete de trabajo o fase del cronograma sin actualización de avance o real en este período; un pronóstico de costo a completar a través de tres fórmulas estándar de la industria para la estimación al finalizar; un registro de exposición por trabajo bloqueado / órdenes de cambio; un registro de compromiso y estado de certificación de subcontratos; un registro de utilización y recuperación de equipo; un registro mensual de actividad de seguridad que agrega inspecciones de obra, charlas de seguridad (toolbox talks), conteos de incidentes y horas por categoría; y una consolidación multi-edificio para una entidad legal que posee más de un edificio (ver más abajo).

**Cierre de obra**, producido una vez que un proyecto o una fase de este se aproxima a su finalización: un marcador operativo ponderado que abarca seis categorías — seguridad, calidad, relaciones con el cliente, control de costos, cronograma y documentación — con una capa de automatización de evidencia que responde el pequeño subconjunto de sus preguntas que un registro real puede confirmar mecánicamente (por ejemplo, si existe un envío mensual requerido para el período), mientras deja marcada exactamente como tal cada pregunta que requiere juicio humano, nunca calificada automáticamente; y un paquete de lista de verificación de cierre de obra y revisión posterior al proyecto.

Dos características del reporte merecen mencionarse porque son inusuales. Primero, el motor calcula sus resultados de abajo hacia arriba a partir de primitivas de paquetes de trabajo — de la misma manera que lo haría el propio software de estimación y programación de un contratista — y luego los concilia contra las estimaciones profesionales preparadas de forma independiente del piloto y sus fechas de cronograma conocidas, que sirven como una hoja de respuestas en lugar de datos que los reportes simplemente reformatean. Segundo, todo reporte se niega a fabricar. Donde no existe una medición real — un costo real sin factura detrás, un porcentaje completado sin avance observado, un conteo de incidentes sin una entrada de registro de seguridad, un segundo edificio bajo una TitleCo que aún no se ha agregado — el reporte imprime un guion largo o un conteo real y honesto de cero-sobre-total, nunca una proyección disfrazada de observación. Un cero en una columna de incidentes sería en sí mismo una afirmación de seguridad; un espacio en blanco es el estado honesto del dato. Varios reportes en los grupos de seguimiento continuo y cierre de obra arriba se renderizan así hoy: el motor subyacente y el plan de cuentas son reales y están probados, y el reporte está estructuralmente listo en el momento en que exista un dato real, pero no ha ocurrido ninguna transacción o envío real todavía para este piloto.

**Por qué importa:** un reporte de este motor es trazable a una entrada real o está visiblemente vacío — no existe un tercer estado, y esa propiedad la aplica la capa de cálculo, no la diligencia del revisor.

---

## Retención, el período de gravamen (lien period) y los plazos estatutarios

Los contratos de construcción están sujetos a retención estatutaria (holdback) — un porcentaje definido de cada pago certificado que el propietario retiene, liberado una vez que expira un período de gravamen durante el cual los proveedores impagos pueden registrar un reclamo contra el edificio, sin que se haya registrado ningún reclamo. El propio plan de cuentas del ledger de dinero rastrea esto por relación contractual, y una política de fondo de retención, nombrada y explícita (un fondo de retención por contrato, versus un fondo agrupado entre todos los oficios de un proyecto) es una decisión configurable que este motor hace visible en lugar de una suposición silenciosa — la pregunta estatutaria real sobre cuál modelo aplica a un proyecto multi-oficio sin un solo contratista principal está, en la jurisdicción en la que se basa este piloto, genuinamente sin resolver por la jurisprudencia, y el motor refleja eso convirtiendo la elección en una configuración nombrada en lugar de tomar partido en silencio. Un umbral de contrato grande que activa un requisito legal de liberación progresiva de la retención de forma escalonada o anual, en lugar de solo al finalizar, también se modela como un hecho estructural que el motor verifica, no un monto de liberación calculado.

El plazo estatutario de pago propiamente dicho — que hace cascada de una fecha de vencimiento real para el pago del propietario y luego para el pago del contratista general a su subcontratista a partir de la fecha en que se recibe una factura adecuada — se calcula en el propio reporte del motor contable hermano; ver [[tool-accounting]] para esa mitad del mecanismo. Los dos motores deliberadamente no duplican esta lógica: la política y el seguimiento del fondo de retención viven aquí, contra el ledger del trabajo físico; la aritmética de fechas estatutarias vive allá, contra el lado monetario de la misma relación.

---

## Servicios de plataforma

Tres piezas del dominio del motor se construyeron deliberadamente como servicios de plataforma independientes en lugar de módulos internos, de modo que aplicaciones y reportes futuros puedan leer los mismos datos sin pasar por este motor: `service-materials`, el almacén canónico de paquetes de trabajo; `service-schedule`, el servicio de cálculo de ruta crítica; y `service-notify`, un vigilante agnóstico al dominio que se dispara ante plazos vencidos e incumplimientos de umbral cuando un llamador reporta una observación. `tool-construction` los consume como un cliente HTTP ordinario — los paquetes de trabajo del piloto viven en `service-materials`, y el reporte de materiales se renderiza a partir de lo que el servicio devuelve, no del archivo que el motor cargó. Cada servicio registra su estado y reconstruye su índice al reiniciar. Los tres se ejecutan localmente hoy; el cableado de supervisión de producción queda pendiente.

**Por qué importa:** los datos de costo, cronograma y alertas viven detrás de límites de servicio que cualquier aplicación futura puede leer, de modo que el motor CLI es un consumidor de los datos de la plataforma, no su dueño.

---

## Topología del producto y el límite gratuito/pago

`tool-construction` es un componente dentro de una familia más amplia: el propio ledger de construcción; el motor contable hermano, [[tool-accounting]], diseñado para recibir un flujo de costo en dólares en un solo sentido desde este; `tool-typeset`, el renderizador de documentos compartido que ahora realiza la renderización de producción para ambos motores; y el motor propuesto [[tool-payroll]], diseñado para recibir un flujo de horas y clase de mano de obra desde el ledger de construcción como tarjetas de tiempo. Los crates piloto específicos para los motores contable y de nómina están estructurados junto al piloto de construcción, pero los flujos entre motores aún no están conectados.

Una pregunta distinta de cualquiera de las anteriores es qué ocurre cuando una entidad legal posee más de un edificio, o cuando un contratista opera más de un proyecto a la vez — dos casos genuinamente diferentes que el motor mantiene separados. Varios edificios bajo una entidad legal, cada uno con su propio costo y cronograma de construcción pero compartiendo un solo conjunto de obligaciones financieras y estatutarias, se maneja dentro del propio reporte de este motor: un ledger de construcción por edificio, consolidándose en lo que sea que el motor contable de esa entidad ya produzca. Un contratista o administrador de propiedades que opera varios desarrollos separados y legalmente distintos a la vez es un caso diferente, una capacidad de toda la plataforma en lugar de algo que este motor construya por sí mismo — ver [[financial-and-construction-tools-overview]] para el tratamiento completo de ambos casos y cómo encaja la capa de consolidación más amplia de la plataforma.

El sustrato de archivo y la terminal de la plataforma son gratuitos (Apache-2.0); la consolidación entre archivos es el límite pago que aplica en toda la plataforma. `tool-construction` está diseñado como una segunda superficie comercial separada sobre eso — lo que se vendería es la propia ingeniería de dominio (el esquema de puntuación de calidad, las matemáticas del valor ganado, el mecanismo del ledger), no un margen sobre una infraestructura que ya es gratuita.

Las reglas de distribución de la plataforma clasifican los componentes `tool-*` como herramientas internas del operador, no distribuidas como producto independiente por defecto, con excepciones otorgadas de forma individual — `tool-wallet` es el único precedente existente. **No hay ninguna excepción de distribución registrada actualmente para `tool-construction`.**

**Por qué importa:** la línea gratuita/pago está en la propia ingeniería de dominio, no en la infraestructura debajo de ella — el mismo principio que mantiene gratuitos el sustrato de archivo y la terminal de la plataforma aplica aquí también, un nivel más arriba.

## Licenciamiento

`tool-construction` está licenciado bajo AGPL-3.0-or-later. AGPL-3.0-or-later es una licencia copyleft: el código fuente está disponible para todos, y cualquier versión modificada — incluida una operada como servicio de red — debe publicarse bajo la misma licencia si se distribuye o se pone a disposición a través de una red. Existe una licencia PointSav-Commercial separada, disponible como alternativa paga para cualquiera que necesite distribuir una versión modificada, u ofrecerla como servicio de red, sin esa obligación copyleft.

**Por qué importa:** un prestamista o el propio ingeniero de un propietario pueden leer y auditar el código fuente completo antes de decidir si confían en él — el código no es una caja negra detrás de un muro de pago.

---

## Lo que aún no está construido

Los límites indicados al inicio merecen repetirse con precisión. Aún no construido: una fuente independiente de medición de cantidad instalada, que condiciona el reporte real de valor ganado; los flujos en un solo sentido hacia [[tool-accounting]] y [[tool-payroll]]; el adaptador de almacenamiento de archivo que persistiría los datos del ledger a través del propio almacén de registros de la plataforma; el mecanismo de transferencia de acceso en la transición de venta; y cualquier superficie de consola o terminal — las dos pantallas propuestas (una vista de tabla del ledger y un panel de paquetes de trabajo/calidad) permanecen sin un espacio de compilación asignado en la terminal de doce teclas de función fija de la plataforma, y la cadena de herramientas se opera enteramente desde la línea de comandos. Tampoco construido: una vista real y prospectiva de consolidación de portafolio solo es significativa una vez que existe un segundo edificio de una entidad legal contra el cual consolidar — la maquinaria de reporte es real y está probada, pero el piloto de hoy tiene exactamente un edificio registrado.

Las preguntas de diseño abiertas que permanecen genuinamente sin resolver incluyen si las aprobaciones de calidad necesitan firma criptográfica dado su peso legal potencial en un reclamo por defecto, si la puntuación de desempeño individual pertenece dentro de este sistema o permanece como una preocupación separada, qué activa y autoriza el mecanismo de transferencia de acceso en la transición de venta y su interfaz con un proceso legal de cierre real, y — una pregunta estatutaria real y sin resolver, no una brecha de ingeniería — si un proyecto multi-oficio sin un solo contratista principal debe mantener un fondo de retención agrupado o un fondo por contrato, una elección que la jurisprudencia aún no resuelve en la jurisdicción en la que se basa este piloto.

**Por qué importa:** ninguna de estas brechas está oculta dentro de una prueba exitosa o un respaldo silencioso — cada una se nombra aquí para que un lector que evalúa el motor sepa con precisión qué afirmaciones están comprobadas hoy y cuáles siguen siendo intención declarada.

## Ver también

- [[tool-accounting]] — el motor contable hermano diseñado para recibir un flujo de costo en un solo sentido desde este ledger, y donde realmente vive el cálculo del plazo estatutario de pago al que se refiere la sección de retención de este artículo
- [[tool-payroll]] — el motor de nómina propuesto, diseñado para recibir un flujo de tarjetas de tiempo en un solo sentido desde este ledger
- [[financial-and-construction-tools-overview]] — el tratamiento completo de la consolidación multi-edificio y multi-proyecto al que se refiere la sección de Topología del producto de este artículo
