---
schema: foundry-doc-v1
title: "Conceptos Generales"
slug: background-index
category: background
type: topic
content_type: topic
index_type: thematic
index_scope: background
quality: complete
short_description: "Conceptos generales de computación definidos desde sus principios — appliances, topología de borde y niebla, diseño de interfaces y la práctica de seguridad que PointSav rechaza — para el lector que quiere el vocabulario del campo antes de que los artículos de la plataforma lo den por sabido."
status: active
bcsc_class: public-disclosure-safe
last_edited: 2026-09-06
editor: pointsav-engineering
paired_with: _index.md
---

**Los artículos de conceptos generales** definen las nociones de computación que el resto de
esta base de conocimiento da por sabidas. Ninguno describe algo que PointSav haya construido:
son el vocabulario propio del campo, redactado desde sus principios para que el lector llegue
a un artículo de arquitectura o de servicios con los términos ya en la mano. Se leen en
cualquier orden, o no se leen: cada artículo de la plataforma se sostiene por sí solo.

Están pensados para hojearse, no para consultarse. Quien busque la definición precisa de un
término de la plataforma debe acudir al [[glossary-documentation|glosario]].

<!-- START-HERE-HIGHLIGHT: el motor lee este bloque para la tarjeta "empezar aquí"
     (reutiliza el componente cluster-card--start-here existente). No añadir más de una. -->

**Empiece aquí:** [[computer-appliance|Electrodoméstico informático]] — el patrón de hardware y software como una única unidad sellada del que descienden las imágenes de appliance de la plataforma, sus sistemas operativos mínimos y sus formatos de despliegue de función única.

<!-- END-START-HERE-HIGHLIGHT -->

## Appliances y sistemas mínimos {#group-count-4}

Unidades informáticas de propósito único y los sistemas operativos reducidos sobre los que corren.

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: appliances-and-minimal-systems -->
- [[computer-appliance|Electrodoméstico informático]] — Dispositivo informático que combina hardware y software para una única función bien definida, implementado como unidad sellada no reutilizable para computación general.
- [[virtual-appliance|Electrodoméstico virtual]] — Imagen de máquina virtual preconfigurada que combina un sistema operativo mínimo con una aplicación específica, distribuida como unidad autónoma para hipervisores compatibles.
- [[just-enough-operating-system|Sistema operativo justo lo necesario]] — Filosofía de sistemas operativos que reduce el SO a los componentes mínimos que necesita una aplicación, recortando superficie de ataque, memoria y mantenimiento.
- [[lightweight-linux-distribution|Distribución Linux ligera]] — Distribución Linux diseñada para usar mucha menos RAM y capacidad de procesador que las distribuciones completas, apta para hardware limitado, embebido o heredado.
<!-- END AUTO-GENERATED -->

## Dónde ocurre el cómputo {#group-count-2}

Los términos de topología distribuida que describen alejar el procesamiento del centro de datos central.

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: where-computation-happens -->
- [[edge-computing|Computación de borde]] — Paradigma de computación distribuida que acerca cómputo y almacenamiento a las fuentes de datos, reduciendo latencia y ancho de banda frente a la nube centralizada.
- [[fog-computing|Computación de niebla]] — Arquitectura distribuida que sitúa cómputo, almacenamiento y servicios de red entre dispositivos de borde y la nube, definida por Cisco en 2012 y estandarizada en IEEE 1934-2018.
<!-- END AUTO-GENERATED -->

## Interfaces y práctica de diseño {#group-count-3}

Cómo un software se dirige a otro, y cómo se diseña para la persona que lo usa.

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: interfaces-and-design-practice -->
- [[application-programming-interface|Interfaz de programación de aplicaciones]] — Interfaz definida que permite la comunicación entre sistemas de software especificando las llamadas disponibles, cómo realizarlas y los formatos de datos intercambiados.
- [[user-interface-design|Diseño de interfaz de usuario]] — Disciplina de diseño de interfaces entre humanos y máquinas orientada a maximizar usabilidad y experiencia de usuario, regida por los principios de la norma ISO 9241.
- [[user-experience-design|Diseño de experiencia de usuario]] — Práctica de diseño multidisciplinar que abarca toda la interacción del usuario con una empresa y sus productos, acuñada por Donald Norman en Apple a inicios de los años 1990.
<!-- END AUTO-GENERATED -->

## Práctica de seguridad {#group-count-1}

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: security-practice -->
- [[security-through-obscurity|Seguridad por oscuridad]] — Dependencia del secreto del diseño o la implementación como mecanismo principal de seguridad, rechazada en la práctica profesional desde el principio de Kerckhoffs de 1883.
<!-- END AUTO-GENERATED -->

## Véase también

- [Glosario y Referencia](/category/reference) — el léxico propio de la plataforma y las normas a las que este wiki somete su escritura
- [Conceptos Fundamentales](/category/substrate) — los mecanismos reutilizables que PointSav construyó, a diferencia de los conceptos del campo definidos aquí
- [Seguridad y Confianza](/category/security) — lo que la plataforma hace en lugar de depender del secreto
