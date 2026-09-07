---
schema: foundry-doc-v1
title: "Monitor an active construction project"
slug: monitor-an-active-construction-project
short_description: "Runs eight independent recurring-cadence report binaries — status, exceptions, cost-to-complete, change-order exposure, subcontract and equipment tracking, safety activity, and portfolio roll-up — against a project's data directory, plus the dashboard that confirms what actually landed on disk."
category: how-to
index_group: financial-construction-tools
content_type: how-to
type: how-to
quality: complete
status: active
audience: "Engineers (hands on keyboard); customer operators"
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: monitor-an-active-construction-project.es.md
research_trail:
  sources: [tool-construction-tco-26/src/bin/status_report.rs, tool-construction-tco-26/src/bin/compliance_exceptions.rs, tool-construction-tco-26/src/bin/cost_to_complete.rs, tool-construction-tco-26/src/bin/change_order_exposure.rs, tool-construction-tco-26/src/bin/subcontract_status.rs, tool-construction-tco-26/src/bin/equipment_utilisation.rs, tool-construction-tco-26/src/bin/safety_activity_summary.rs, tool-construction-tco-26/src/bin/portfolio_rollup.rs, tool-construction-tco-26/src/bin/report_index.rs]
  verification_method: "confirmed the real binary names, the shared TCO26_DATA_DIR env var, and each report's real output filename directly against the eight binaries' own source; confirmed via report_index.rs's own sections() function that these eight are exactly the real 'Monthly' dashboard grouping, not an invented category"
---

## Prerequisites

- Everything in [[generate-a-construction-cost-estimate]]'s prerequisites — a working Rust toolchain, a checkout of the workspace, and `TCO26_DATA_DIR` pointed at a real project's data directory.
- The project's kick-off reports (cost estimate, schedule, work packages) already generated at least once — several of the reports below read the same source files.
- No services need to be running for any of the eight report binaries below. (This is distinct from the kick-off pipeline's `calibrate`/`solve_rate`/`post_ledger` binaries, which do depend on archive-resident services — see [[tool-construction]]'s Platform services section.)

## Purpose

Produce the recurring-cadence reports a project manager, owner, or lender reviews on an ongoing basis while a project is under construction: overall status, data-currency exceptions, cost-to-complete forecasting, change-order exposure, subcontract and equipment tracking, a safety-activity register, and — where the project's legal entity owns more than one building — a roll-up across them.

Each of the eight reports below is its own independent binary. There is no single "run everything" command; run the ones relevant to the review cadence at hand.

## Procedure

### 1. Point every binary at the same data directory

```bash
export TCO26_DATA_DIR=/data/construction/example-project
```

Every binary in this guide reads this same environment variable — the same one the kick-off reports use. Nothing here introduces a second, differently named variable.

### 2. Run the report(s) you need

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

Each is a complete, independent invocation — none takes a flag, and running one does not require having run any other first. Each prints one structural-count summary line to stdout and writes its own HTML and PDF into `<data-dir>/outputs/<year>/`, following the same naming convention as the report binary itself (for example, `status_report` writes `status_report.html`/`.pdf`).

### 3. Check what's present with the dashboard

```bash
cargo run --bin report_index -p tool-construction-tco-26
```

This renders a single navigation page (`<data-dir>/outputs/<year>/gp26-report-index.html` on the reference deployment — the filename itself carries a deployment-specific prefix, matching the crate's own per-deployment naming) listing every report this engine can produce, grouped by the same kick-off / monthly / job-completion cadence used in this guide, with a live-checked status column: a report that has never been generated shows as "Not yet generated" rather than a broken link. Run this after generating any subset of reports to confirm what actually landed on disk.

## Expected outcome

| Report | Real output |
|---|---|
| Monthly project status report | Budget, schedule, and progress figures traced to real inputs; anything unmeasured renders as an en dash |
| Data-currency exception register | Every work package or schedule phase with no actual or progress update this period |
| Cost-to-complete forecast | Three industry-standard estimate-at-completion formulas, each rendering an en dash rather than a number wherever the actual-cost or earned-value input it needs hasn't been measured yet |
| Change-order exposure register | Work packages at zero remaining unencumbered budget, and work packages with no ledger posting at all yet |
| Subcontract commitment / certification status | Claimed-versus-certified figures for every subcontracted work package |
| Equipment utilisation / recovery | Operated-versus-idle hours and the resulting productivity variance for every equipment-coded work package |
| Safety-activity register | Monthly counts across the categories a real site-safety program tracks — inspections, toolbox talks, incident severity, hours worked |
| Portfolio roll-up | One row per building this legal entity owns, plus a total — one row today on the reference deployment, since it currently has exactly one building on file |

## Verification

- **Open each PDF and look at it**, the same discipline [[generate-a-construction-cost-estimate]] describes for the kick-off reports — a passing build is not the same claim as a readable page.
- **A report that shows every figure as an en dash, or a table with zero rows, is not necessarily broken.** Several of these reports are structurally ready and fully tested but have nothing real to show yet on a project that hasn't reached the relevant milestone — a project with no safety submission on file this month should show an empty safety register, not a fabricated one. Read each report's own "basis of preparation" note (printed on the report itself) before treating an empty result as a defect.
- **Re-run the dashboard** after generating a batch of reports to confirm the count of "present" reports matches what you expect.

## What this task does not do

- **It does not auto-score judgment calls.** The job-completion scorecard covered in [[close-out-a-construction-project]] is the clearest example, but the same rule applies here: no report in this family ever turns a human judgment question into an automated yes/no.
- **It does not compute a safety incident rate.** The safety-activity register reports real counts only; a rate calculation (incidents per hours worked) depends on a jurisdiction-specific normalization convention that has not been independently verified, so none is computed.
- **It is not yet multi-project across separate developments.** The portfolio roll-up covers multiple buildings under one legal entity — see [[financial-and-construction-tools-overview]] for why that's a different case from a contractor running several separate developments at once.

## Edge cases

- **A report whose underlying CSV has a header that doesn't match its expected schema fails to load**, the same fail-loud convention every report in this family follows — never a silent partial read.
- **The portfolio roll-up's total is left blank (not zero) if any one building's figure is unmeasured** — a total is only shown when every input to it is real, so a genuinely unmeasured building never silently drops out of the total as if it contributed zero.

## Rollback

Nothing to undo in the source data — every binary in this guide reads existing files and writes only into `<data-dir>/outputs/<year>/`. Delete the output files, or re-run to replace them.

## Next steps

- [[close-out-a-construction-project]] — the job-completion reports this guide's cadence eventually leads to
- [[generate-a-construction-draw-workbook]] — the sibling accounting-side reports covering the same project's statutory and payment-timing exposure

## See also

- [[tool-construction]] — the ledger design and the full report list these binaries render from
- [[generate-a-construction-cost-estimate]] — the kick-off reports several of these read from
