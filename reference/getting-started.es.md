---
schema: foundry-doc-v1
title: "Comenzando con la plataforma PointSav"
slug: getting-started
category: reference
index_group: platform-orientation
type: concept
content_type: topic
quality: stub
status: active
audience: vendor-public
bcsc_class: public-disclosure-safe
language_protocol: PROSE-TOPIC
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: getting-started.md
short_description: "Una orientación a la plataforma de desarrollo PointSav: qué es, para quién es, por dónde empezar y cómo encajan las piezas antes de la primera tarea."
aliases:
  - quick-start
cites: []
---

La plataforma PointSav es una pila de software verificable de forma independiente y controlada por el operador, para inteligencia en bienes raíces comerciales, gestión de flotas y cómputo distribuido. Esta guía orienta a nuevos colaboradores y evaluadores hacia las superficies principales de la plataforma y la estructura de la documentación.

## Por dónde empezar

- **Plataforma de desarrollo** — el [[guide-catalog|Catálogo de guías para desarrolladores]] enumera guías prácticas de tipo "cómo hacer" agrupadas por tarea.
- **Arquitectura** — [[ppn-small-business-compute|PPN Small-Business Compute]] y [[ppn-vm-resource-pool|Arquitectura del pool de recursos de VM de PPN]] presentan el sustrato de cómputo.
- **Modelo de autorización** — [[machine-based-auth|Autorización basada en máquina]] describe el emparejamiento como permiso, el modelo de identidad de dispositivo usado en toda la plataforma.
- **Datos y GIS** — [[app-orchestration-gis|Plataforma de orquestación GIS]] cubre el motor de inteligencia de ubicación.

## Requisitos previos

- Acceso a un nodo de la Red Privada PointSav (PPN) mediante aprobación de emparejamiento. Ver [[machine-based-auth|Autorización basada en máquina]] para el modelo de emparejamiento.
- Familiaridad con herramientas de línea de comandos. La plataforma no tiene instalador gráfico.

## Primeros pasos

Para un ingeniero que abre la plataforma por primera vez, una sesión de trabajo depende de que encajen cuatro elementos: acceso alcanzable al nodo a través del túnel WireGuard y el controlador de flota; familiaridad con la pila de cómputo de tres nodos descrita en [[ppn-small-business-compute|PPN Small-Business Compute]] (controlador de flota, agente por nodo, proxy de inquilino); la capacidad de generar una VM a través del proxy de inquilino; y la Consola del SO, la interfaz de terminal usada para las VMs aprovisionadas y la gestión de la plataforma. La versión ejecutable, paso a paso, de esta ruta está en el [[guide-catalog|Catálogo de guías para desarrolladores]].

Esta orientación cubre por dónde empezar y los requisitos previos para una primera sesión de trabajo; un recorrido más completo de las superficies de la plataforma aún no está escrito.
