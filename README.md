# State of Fitness — Prompt Library

This repository contains a prompt library designed to automate common workflows at State of Fitness, a personal training studio in South Yarra. It was developed for La Trobe University BUS4005 Assessment 1.

---

## Purpose

State of Fitness currently spends about five hours each week on manual copywriting, repeated client messages and enquiry handling.

This library includes 10 prompts across three connected workflows. The prompts are designed to reduce repetitive work while keeping a staff member involved before any client-facing content is sent.

## Workflow map

| Phase | Workflow                                  | Prompts |
| ----- | ----------------------------------------- | ------- |
| 1     | Website audit and lead capture            | P1–P4   |
| 2     | New member and client onboarding          | P5–P7   |
| 3     | Client journey and recurring member tasks | P8–P10  |

### Phase 1 — Website audit and lead capture

| ID | Task                                      | Chain             |
| -- | ----------------------------------------- | ----------------- |
| P1 | Conversion copy audit                     | Step 1 — analyse  |
| P2 | Hero section, headline and CTA rewrite    | Step 2 — generate |
| P3 | Brand voice and claims compliance check   | Step 3 — verify   |
| P4 | Inbound enquiry triage and response draft | Standalone        |

### Phase 2 — New member and client onboarding

| ID | Task                                                            | Dependency |
| -- | --------------------------------------------------------------- | ---------- |
| P5 | Convert an intake form into a structured client profile         | —          |
| P6 | Create a welcome sequence and first-session preparation message | Uses P5    |
| P7 | Create a goal-setting brief                                     | Uses P5    |

### Phase 3 — Client journey and recurring member tasks

| ID  | Task                                                               | Dependency |
| --- | ------------------------------------------------------------------ | ---------- |
| P8  | Create scheduled check-in and milestone messages                   | —          |
| P9  | Draft responses to enquiries, complaints and cancellation requests | —          |
| P10 | Identify at-risk members and draft re-engagement messages          | —          |

**Chained module:** P1 → P2 → P3 forms a three-step process: analyse, generate and verify. The full process is documented in `/chain-modules/website-copy-chain.md`.

---

## Design principles

The prompt library follows five main design principles:

* **Modularity** — each prompt focuses on one task. Prompts can be used on their own or combined into a larger workflow.
* **Documentation** — each prompt records its purpose, required inputs, output format, dependencies, fallback behaviour and limitations.
* **Versioning** — each prompt includes at least three versions, with notes explaining what changed and why. The Git commit history provides an additional record of changes.
* **Testing** — each version is tested in La Trobe Prompt Lab for clarity, constraint handling, verifiability and hallucination risk. The results are recorded in the relevant prompt file.
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
