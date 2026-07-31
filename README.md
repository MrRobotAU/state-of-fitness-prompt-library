# State of Fitness — Prompt Library

This repository contains a prompt library designed to automate common workflows at State of Fitness, a personal training studio in South Yarra. It was developed for La Trobe University BUS4005 Assessment 1.

---

## Purpose

State of Fitness currently spends about five hours each week on manual copywriting, repeated client messages and enquiry handling.

This library includes 10 prompts across three connected workflows. The prompts are designed to reduce repetitive work while keeping a staff member involved before any client-facing content is sent.

## Workflow map

| Phase | Workflow | Prompts |
| ----- | ----------------------------------------- | ------- |
| 1 | Website audit and lead capture | P1–P4 |
| 2 | New member and client onboarding | P5–P7 |
| 3 | Client journey and recurring member tasks | P8–P10 |

### Phase 1 — Website audit and lead capture

| ID | Task | Chain |
| -- | ----------------------------------------- | ----------------- |
| P1 | Conversion copy audit | Step 1 — analyse |
| P2 | Hero section, headline and CTA rewrite | Step 2 — generate |
| P3 | Brand voice and claims compliance check | Step 3 — verify |
| P4 | Inbound enquiry triage and response draft | Standalone |

### Phase 2 — New member and client onboarding

| ID | Task | Dependency |
| -- | --------------------------------------------------------------- | ---------- |
| P5 | Convert an intake form into a structured client profile | — |
| P6 | Create a welcome sequence and first-session preparation message | Uses P5 |
| P7 | Create a goal-setting brief | Uses P5 |

### Phase 3 — Client journey and recurring member tasks

| ID | Task | Dependency |
| --- | ------------------------------------------------------------------ | ---------- |
| P8 | Create scheduled check-in and milestone messages | — |
| P9 | Draft responses to enquiries, complaints and cancellation requests | — |
| P10 | Identify at-risk members and draft re-engagement messages | — |

**Chained module:** P1 → P2 → P3 forms a three-step process — analyse, generate, verify. Each step is documented in its own file in `phase-1-lead-capture/`. P1 returns a JSON audit that P2 consumes directly as its input, and P3 acts as a verification gate before anything is published.

---

## Design principles

The prompt library follows five main design principles:

* **Modularity** — each prompt focuses on one task. Prompts can be used on their own or combined into a larger workflow.
* **Documentation** — each prompt records its purpose, required inputs, output format, dependencies, fallback behaviour and limitations.
* **Versioning** — each prompt is developed across multiple versions, with notes explaining what changed and why. The Git commit history provides an additional record of changes.
* **Testing** — each version is tested in La Trobe Prompt Lab for clarity, constraint handling, structure, verifiability and hallucination risk. The results are recorded in the relevant prompt file.
* **Governance** — every prompt includes a human review step. No client-facing content should be sent without approval from the owner or a trainer.

## Evaluation

Each prompt is assessed using five criteria:

* accuracy
* consistency
* business fit
* clarity
* ethical alignment

Prompt Lab scores and review notes are recorded in each prompt file.

## Data handling

All examples in this repository are synthetic.

The repository does not include real client names, contact details, health information, injury information or intake responses. Prompts use template variables such as `{client_name}`, `{goal}` and `{tenure_months}` and are intended to run on de-identified information.

## Repository structure

```
├── phase-1-lead-capture/
│   ├── Evidence/
│   ├── P1-conversion-audit.md
│   ├── P2-hero-rewrite.md
│   ├── P3-compliance-check.md
│   └── P4-enquiry-triage.md
├── phase-2-onboarding/
│   ├── Evidence/
│   ├── P5-intake-profile.md
│   ├── P6-welcome-sequence.md
│   └── P7-goal-brief.md
└── phase-3-member-journey/
    ├── Evidence/
    ├── P8-checkin-milestone.md
    ├── P9-complaint-handler.md
    └── P10-at-risk-reengagement.md
```

Each phase folder contains an `Evidence` folder holding the Prompt Lab checkpoint screenshots for the prompts in that phase.

## Version convention

Each prompt was developed across two to four versions. The number varies by prompt, because iteration stopped where measurable improvement flattened rather than at a fixed count.

* **v1** — the initial attempt, written without a structured framework
* **v2** — the RACE framework applied (role, action, context, expectation), with constraints and a defined output format
* **v3 and v4** — further refinement where testing showed it was still producing gains

P1 is the only prompt taken to a fourth version, where the self-defined assessment criteria were replaced with an established external framework.

## How to read this repository

Each prompt file contains every version of that prompt, a short note explaining what changed between versions and why, and the Prompt Lab review scores for each one. The checkpoint comparison screenshots sit in the `Evidence` folder of the relevant phase.

If you are reviewing a single example, `phase-1-lead-capture/P1-conversion-audit.md` has the most detailed iteration history.

A note on the scores: Prompt Lab assesses how a prompt is constructed, not whether its output is fit to send. Several prompt files record cases where a version scored acceptably while producing an output that was not usable. Those observations are recorded alongside the scores rather than in place of them.
