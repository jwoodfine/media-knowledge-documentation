---
schema: foundry-doc-v1
title: "The financial and construction tool family — a shared design across three products"
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
paired_with: financial-and-construction-tools-overview.es.md
short_description: "How tool-accounting, tool-construction, and tool-payroll relate as one product family — a shared double-entry design, one-way data feeds between them, and a shared free/paid architecture boundary."
cites: []
---

[[tool-accounting]], [[tool-construction]], and [[tool-payroll]] are three separate products that share one design lineage rather than three independent tools that happen to sit near each other. This article covers what they share and how they connect; each tool's own article covers its own domain in depth.

## One double-entry design, three domains

All three tools are built on, or designed around, the same double-entry ledger discipline: every posting is a balanced entry, nothing is stored that can be derived from what is already recorded, and a full, tamper-evident history of every entry is kept rather than overwritten. `tool-accounting` applies this discipline to financial statements. `tool-construction` applies the identical discipline to physical construction quantities alongside dollars, in a two-ledger design (a production ledger for quantities, a cost ledger for dollars) built specifically because construction cost tracking needs both at once. `tool-payroll` is designed to apply the same underlying discipline to gross-to-net pay calculation and statutory remittance timing.

**Why it matters:** a shared design means a fix or an improvement to the underlying ledger mechanics is intended to benefit all three tools, not just one, and a developer or auditor who understands one tool's ledger model already understands the shape of the other two.

## How data moves between them — one-way feeds only

The three tools are designed to connect through one-way data bridges, never a shared table and never a value converted back to its source:

- **`tool-construction` → `tool-accounting`**: dollar cost, feeding the owner's financial statements as construction-in-progress.
- **`tool-construction` → `tool-payroll`**: hours and labour class, feeding payroll as timecards. This bridge is designed to run one way only — dollars come back only as ordinary payroll and payable postings into the construction cost ledger, through the same review path as any other source transaction, never as an automatic conversion of hours through a rate. That gap between an hours-times-rate estimate and real payroll dollars is a deliberate design feature, not an omission: it is the labour rate variance, and closing the loop automatically would destroy the very signal it exists to surface.

**Why it matters:** an owner or auditor evaluating these tools together does not need to reconcile numbers between them by hand — the one-way design means each tool's own ledger stays the authoritative source for its own domain, and every other tool receives it as a dated input, never as a shared mutable value.

## Shared free/paid architecture boundary

All three tools sit on the same underlying platform architecture: the archive substrate and the terminal that hosts them are free (Apache-2.0); cross-archive aggregation — the one capability an isolated archive genuinely cannot perform for itself — is the platform-wide paid boundary. Each of the three tools is designed as its own separate, additional commercial surface on top of that shared boundary, selling the domain engineering itself (the accounting engine, the construction ledger mechanics, the payroll calculation engine) rather than a markup on infrastructure that is already free to run.

## Licensing

`tool-accounting`, `tool-construction`, and `tool-payroll` are licensed under
AGPL-3.0-or-later. A separate PointSav-Commercial license is available as a paid
alternative for anyone who needs to distribute a modified version, or offer it as a
network service, without the copyleft obligation.

## Build status, side by side

| Tool | Real state today |
|---|---|
| `tool-accounting` | Furthest along on its original toolchain: real code, built and run against real historical data end-to-end for statement production, with consolidation now wired. A second, construction-industry pilot toolchain built on the same core library produces draw-workbook and statutory-compliance reporting for an active build. |
| `tool-construction` | Real code, running against a live pilot: the full quantity-side ledger and a genuine money-denominated cost ledger are both built, driving more than a dozen real reports across kick-off, ongoing-monitoring, and job-completion cadences. Estimate and schedule data are real; actual-cost, safety, and job-completion data are structurally ready but not yet populated, because the pilot has not yet reached the point where that data exists. |
| `tool-payroll` | One real report built and running — a division-level payroll register aggregating budgeted labour hours under a cited jurisdiction's wage-timing rules. Gross-to-net pay, pay frequency, and remittance computation remain design-only. |

**Why it matters:** the three tools are frequently discussed together because of their
shared design, but they are not at the same stage of readiness — read each tool's own
article for the detail behind this summary before treating any of the three as
describing a finished product.

## Multi-building and multi-project aggregation

Two genuinely different situations both get called "aggregation," and the platform treats them differently on purpose.

**Several buildings under one legal entity** — a common real-estate structure, where one entity holds title to more than one building — share that entity's single accounting engine, since statutory declarations, financial statements, and draw-workbook reporting are obligations of the entity, not of any one building. But each building keeps its own construction engine, because two buildings on the same site can have completely different trade mixes, cost codes, and schedules even though they answer to the same books. `tool-construction` itself produces the roll-up view across an entity's buildings; see its own article for detail.

**A contractor or property manager running several separate, legally distinct developments at once** is a different case entirely, and it is not something any individual tool in this family builds for itself. Querying or comparing data across genuinely separate archives — "which trade partner's defect rate is rising across our whole portfolio," "which of our projects is behind schedule relative to the others" — is a platform-wide capability, sold separately from any one domain engine. Two real components exist in this space today, at different stages: `app-orchestration-bim`, which performs this kind of aggregation for building-information-model data across properties, is built but not yet deployed; a comparable aggregation layer for the accounting and construction domains, referred to under the working name `app-orchestration-accounting`, is a proposed name and scope only — nothing under that name has been built yet.

**Why it matters:** an owner or investor evaluating one building, or one legal entity's holdings, gets that view from the tools in this family directly. An operator running many separate developments at once should expect the fuller cross-portfolio view to come from a separate, platform-level product, not from any one domain engine growing that capability internally.

## See also

- [[tool-accounting]]
- [[tool-construction]]
- [[tool-payroll]]
- [[legal-and-ip-structure]] — the full corporate licensing-tier rationale this article's Licensing section summarizes
