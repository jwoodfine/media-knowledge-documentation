---
schema: foundry-doc-v1
title: "Close out a construction project"
slug: close-out-a-construction-project
short_description: "Runs the two job-completion report binaries — a weighted six-category operational scorecard and a job-completion checklist and review pack — either of which can also be run as a live status check on a project still under construction."
category: how-to
index_group: financial-construction-tools
content_type: how-to
type: how-to
quality: complete
status: active
audience: "Engineers (hands on keyboard); customer operators"
last_edited: 2026-09-07
editor: pointsav-engineering
paired_with: close-out-a-construction-project.es.md
research_trail:
  sources: [tool-construction-tco-26/src/bin/ksf_evidence.rs, tool-construction-tco-26/src/bin/job_closeout.rs, tool-construction-tco-26/src/compute/ksf_evidence.rs, tool-construction-tco-26/src/compute/job_closeout.rs]
  verification_method: "confirmed the real binary names, shared TCO26_DATA_DIR env var, and both reports' honest 'never claims completion' behavior directly against source — in particular, confirmed the job-completion pack cites live, in-progress figures from sibling reports explicitly labeled as snapshots, never as final close-out numbers"
---

## Prerequisites

- Everything in [[monitor-an-active-construction-project]]'s prerequisites.
- The ongoing-monitoring reports (particularly cost-to-complete and change-order exposure) generated at least once — the job-completion pack cites their figures directly rather than recomputing them.

## Purpose

Produce the two reports a project manager or operations lead uses when a project is approaching completion: a weighted operational scorecard across six categories, and a job-completion checklist and review pack. Neither report requires the project to actually be finished to run — both are designed to be usable as a live status check throughout the project, and both say so honestly if the project is still under construction.

## Procedure

### 1. Point both binaries at the project's data directory

```bash
export TCO26_DATA_DIR=/data/construction/example-project
```

### 2. Run the operational scorecard

```bash
cargo run --bin ksf_evidence -p tool-construction-tco-26
```

This renders a full weighted scorecard — six categories (safety, quality, client relationships, cost control, schedule, documentation), each with its own set of real questions and point weightings. A small number of questions in the safety category are automatically evidenced from the safety-activity register covered in [[monitor-an-active-construction-project]] — for example, whether a required monthly submission exists on file for a given period is a fact the register can confirm mechanically. Every other question — which is most of them — is explicitly marked as requiring human review. No question is ever auto-scored based on a judgment call this engine has no way to observe.

### 3. Run the job-completion pack

```bash
cargo run --bin job_closeout -p tool-construction-tco-26
```

This renders a full job-completion checklist (dozens of real close-out items — final inspections, warranty documentation, key handover, and similar) and a structured project-review template. A small number of checklist items that map onto figures the engine already computes — a final cost projection, a summary of change-order exposure — cite those figures directly from the reports covered in [[monitor-an-active-construction-project]], each one explicitly labeled as a current, in-progress snapshot rather than a final number. Every other item is a plain fill-in template, since most close-out actions (returning keys, cancelling insurance, obtaining a utility-transfer confirmation) are physical-world actions this engine has no way to observe.

## Expected outcome

Two files each, `ksf_evidence.{html,pdf}` and `job_closeout.{html,pdf}`, written to `<data-dir>/outputs/<year>/`.

## Verification

- **Neither report ever claims the project is complete on its own authority.** If you run these against a project that is still under construction, both reports say so plainly — the job-completion pack in particular states outright, in its own basis-of-preparation note, that it is rendering against an in-progress project. If a report you generate reads as though it's asserting completion, that's a defect worth reporting, not an expected outcome.
- **Open both PDFs and confirm every cited figure is labeled as a snapshot, not a final number**, wherever a checklist item or scorecard question cites another report's data.
- **Re-run [[monitor-an-active-construction-project]]'s dashboard** afterward to confirm both new files appear under the "Job Completion" section.

## What this task does not do

- **It does not score judgment questions.** The overwhelming majority of the scorecard's questions, and several close-out checklist items, require a human reviewer's own assessment. Nothing in either report attempts to answer them.
- **It does not compute a final cost or a final change-order figure.** Both reports cite the current, in-progress figures from the ongoing-monitoring reports — running this task does not itself close the books on a project.
- **It does not require the project to be finished.** Both binaries run against any project state, at any point — there is no "the project isn't done yet" error condition.

## Edge cases

- **A project with no real data behind the reports it cites still renders a complete report** — every checklist item and scorecard question is present, with the citation fields showing an honest "unmeasured" state rather than being omitted.

## Rollback

Nothing to undo — both binaries read existing files and write only into `<data-dir>/outputs/<year>/`.

## Next steps

- [[monitor-an-active-construction-project]] — the reports these close-out reports cite

## See also

- [[tool-construction]] — the ledger design and the full report list, including these two
