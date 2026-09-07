---
schema: foundry-doc-v1
title: "Background"
slug: background-index
category: background
type: topic
content_type: topic
index_type: thematic
index_scope: background
quality: complete
short_description: "General computing concepts defined from first principles — appliances, edge and fog topology, interface design, and the security practice PointSav rejects — for a reader who wants the field vocabulary before the platform articles use it."
status: active
bcsc_class: public-disclosure-safe
last_edited: 2026-09-06
editor: pointsav-engineering
paired_with: _index.es.md
---

**Background articles** define the general computing concepts the rest of this knowledge base
takes for granted. None of them describes anything PointSav built — they are the field's own
vocabulary, written up from first principles so a reader can arrive at an architecture or
services article already holding the terms it uses. Read them in any order, or read none of
them: every platform article stands on its own.

These are meant to be browsed, not consulted. A reader who wants one platform term defined
precisely should go to the [[glossary-documentation|glossary]] instead.

<!-- START-HERE-HIGHLIGHT: engine reads this block to render the single "start here" card
     (reuses the existing cluster-card--start-here component). Do not add more than one. -->

**Start here:** [[computer-appliance|Computer appliance]] — the hardware-and-software-as-one-sealed-unit pattern that the platform's own appliance images, minimal operating systems, and single-function deployment shapes all descend from.

<!-- END-START-HERE-HIGHLIGHT -->

## Where to start

Ten articles define field vocabulary the rest of this knowledge base assumes. These three carry the most weight elsewhere: the appliance shape, the topology term, and the practice the platform's security articles are written against.

- [[computer-appliance|Computer appliance]] — Hardware and software engineered as one sealed, single-purpose unit — the shape every PointSav appliance image descends from.
- [[edge-computing|Edge computing]] — Why computation moves out to where data is produced. The boundary-ingest and fleet articles use the term without redefining it.
- [[security-through-obscurity|Security through obscurity]] — Rejected in professional practice since Kerckhoffs in 1883, and worth reading first because the platform's security posture is argued in opposition to it.

## Appliances and minimal systems {#group-count-4}

Purpose-built computing units and the stripped-down operating systems they run on.

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: appliances-and-minimal-systems -->
- [[computer-appliance|Computer appliance]] — Computing device pairing hardware and software engineered for one well-defined function, deployed as a sealed unit that cannot be repurposed for general computing.
- [[virtual-appliance|Virtual appliance]] — Pre-configured virtual machine image combining a minimal operating system with a specific application, distributed as a self-contained unit for compatible hypervisors.
- [[just-enough-operating-system|Just enough operating system]] — Operating system philosophy that reduces the OS to the minimum components a specific application needs, shrinking attack surface, memory footprint, and maintenance.
- [[lightweight-linux-distribution|Lightweight linux distribution]] — Linux distribution engineered to use far less RAM and processor capacity than full-featured distributions, suited to constrained, embedded, and legacy hardware.
<!-- END AUTO-GENERATED -->

## Where computation happens {#group-count-2}

The distributed-topology terms for moving processing away from a central data centre.

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: where-computation-happens -->
- [[edge-computing|Edge computing]] — Distributed computing paradigm that places computation and storage near data sources, cutting latency and bandwidth versus centralized cloud data centers.
- [[fog-computing|Fog computing]] — Distributed architecture placing compute, storage, and network services between edge devices and the cloud, defined by Cisco in 2012 and standardized as IEEE 1934-2018.
<!-- END AUTO-GENERATED -->

## Interfaces and design practice {#group-count-3}

How software is addressed by other software, and how it is designed for the person using it.

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: interfaces-and-design-practice -->
- [[application-programming-interface|Application programming interface]] — Defined interface that lets software systems communicate by specifying the available calls, how to make them, and the data formats exchanged.
- [[user-interface-design|User interface design]] — Discipline of designing human-machine interfaces to maximize usability and user experience, governed by the dialogue principles of the ISO 9241 standard.
- [[user-experience-design|User experience design]] — Multidisciplinary design practice covering every aspect of a user's interaction with a company and its products, coined by Donald Norman at Apple in the early 1990s.
<!-- END AUTO-GENERATED -->

## Security practice {#group-count-1}

<!-- AUTO-GENERATED MEMBERSHIP: DO NOT EDIT BELOW — regenerate from index_group: security-practice -->
- [[security-through-obscurity|Security through obscurity]] — Reliance on secrecy of design or implementation as a primary security mechanism, rejected in professional practice since Kerckhoffs' principle of 1883.
<!-- END AUTO-GENERATED -->

## See also

- [Glossary and Reference](/category/reference) — the platform's own lexicon, plus the standards this wiki is written to
- [Core Concepts](/category/substrate) — the reusable mechanisms PointSav built, as distinct from the field concepts defined here
- [Security and Trust](/category/security) — what the platform does instead of relying on secrecy
