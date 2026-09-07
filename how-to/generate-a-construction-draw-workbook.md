---
schema: foundry-doc-v1
title: "Generate a construction draw workbook"
slug: generate-a-construction-draw-workbook
short_description: "Runs the five report binaries in tool-accounting-tco-26's construction-industry extension — capital call request and schedule, statutory declaration, checks issued, and cash-flow calendar — covering an active build's statutory and payment-timing exposure."
category: how-to
index_group: financial-construction-tools
content_type: how-to
type: how-to
quality: complete
status: active
audience: "Engineers (hands on keyboard); customer operators"
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: generate-a-construction-draw-workbook.es.md
research_trail:
  sources: [tool-accounting-tco-26/src/bin/capital_call_request.rs, tool-accounting-tco-26/src/bin/capital_call_schedule.rs, tool-accounting-tco-26/src/bin/statutory_declaration.rs, tool-accounting-tco-26/src/bin/checks_issued.rs, tool-accounting-tco-26/src/bin/cash_flow_calendar.rs]
  verification_method: "confirmed the real binary names and the shared TCO26_DATA_DIR env var directly against all 5 binaries' own source; confirmed the equity-capital-call reframing directly against the compute layer's own real logic rather than assuming it from a description"
---

## Prerequisites

- A working Rust toolchain and a checkout of the workspace containing the accounting crates.
- A data directory with the shared entity/account/consolidation registry populated for the entity these reports run against, plus real budget-lock data from the sibling construction engine (see [[tool-construction]]).
- No services need to be running for any of the five binaries below.

## Purpose

Produce the reporting package an equity investor's independent directors, or a construction lender where one exists, review during an active build: what's being requested against the project's approved budget, the statutory declaration required under construction-lien law, a record of payments actually disbursed, and a calendar of statutory payment due dates cascading from real invoice-receipt dates.

This guide is distinct from [[generate-a-financial-statement-package]], which covers a different, entity-level statement toolchain — this one is specific to an active construction project's own draw/capital-call cycle.

## Procedure

### 1. Point every binary at the shared data directory

```bash
export TCO26_DATA_DIR=/data/construction/example-project
```

The same environment variable the construction-side reports use — this toolchain reads from the same data directory, not a separate one.

### 2. Run the reports

```bash
cargo run --bin capital_call_request -p tool-accounting-tco-26
cargo run --bin capital_call_schedule -p tool-accounting-tco-26
cargo run --bin statutory_declaration -p tool-accounting-tco-26
cargo run --bin checks_issued -p tool-accounting-tco-26
cargo run --bin cash_flow_calendar -p tool-accounting-tco-26
```

Each is independent — running one does not require having run any other first, and each writes its own HTML and PDF into `<data-dir>/outputs/<year>/`.

## Expected outcome

| Report | Real output |
|---|---|
| Capital call request | The current request to the entity's independent directors, sized against the real approved budget |
| Capital call schedule | The running Original Estimate / Revised Estimate / Costs Completed to Date / Cost to Complete / % Complete / Holdback register a capital call draws from |
| Statutory declaration | The sworn lien/holdback compliance attestation the underlying construction-lien statute requires |
| Checks issued | A disbursement register keyed off the real cash account — genuinely empty until a real payment has been disbursed |
| Cash-flow calendar | A calendar of owner-payment and subcontractor-payment due dates, each cascaded from a real invoice-received date |

## Verification

- **Open each PDF and look at it.**
- **A genuinely empty register is not a defect.** On a project with no real payroll, invoice, or payment ever posted, every one of these five reports correctly renders with real, honest zero balances or an empty table — not a fabricated figure. Read each report's own basis-of-preparation note before treating an empty result as broken.
- **Confirm the capital call reports never use lender language** (a "draw request" to a bank) if the entity you're running this against is equity-financed — the report's own framing follows the entity's real financing structure rather than defaulting to loan terminology.

## What this task does not do

- **It does not compute the statutory due-date cascade from anything other than a real, recorded invoice-received date.** A blank invoice-received date produces no calendar entry for that transaction, not an estimated one.
- **It does not disburse or authorize a payment.** These reports read the ledger; nothing in this task writes a payment or a check.

## Edge cases

- **A project with no real transactions posted yet** produces a complete, real set of five reports, all correctly showing empty balances — this is the expected state for a project at kick-off, not an error.

## Rollback

Nothing to undo — every binary reads existing files and writes only into `<data-dir>/outputs/<year>/`.

## Next steps

- [[monitor-an-active-construction-project]] — the construction-side reports this workbook's figures ultimately trace back to

## See also

- [[tool-accounting]] — the construction-industry extension section describing this toolchain's design
- [[tool-construction]] — the retainage and holdback model this workbook's statutory declaration and cash-flow calendar depend on
- [[generate-a-financial-statement-package]] — the entity-level statement toolchain this guide is distinct from
