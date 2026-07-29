# P1 — Conversion copy audit

**Workflow:** Website audit and lead capture
**Problem:** Copy changes are made on instinct, with no way to tell what helped
**Automation potential:** Runs on any page before a rewrite; output feeds P2
**Risks:** Can describe copy that isn't there; may push unsupported health claims
**Human checkpoint:** Owner reviews findings before the rewrite runs

Prompt Lab scores five dimensions. Higher is better on all five — a hallucination
score of 95 means low risk.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | 85 | 60 | 50 | 70 | 90 | 73.5 |
| v2 | 75 | 85 | 80 | 90 | 95 | 83.75 |
| v3 | 80 | 90 | 85 | 90 | 95 | 87.5 |
| v4 | | | | | | |

---

## v1

```
Can you review the copy on my gym website homepage and tell me how to make it better?

[full homepage copy pasted]
```

**73.5** — clarity 85 · constraints 60 · structure 50 · verifiability 70 · hallucination 90

No role, no constraints, no output format. The review flagged constraints (60) and
structure (50) as the weak points — no target audience, no stated goal, no
segmentation of what to assess. Verifiability sat at 70 because the copy was
supplied but no criteria were given to judge it against.

---

## v2 — RACE applied

```
ROLE
You are a conversion copywriter who specialises in boutique fitness and personal
training studios.

TASK
Audit the homepage copy supplied below and identify what is preventing visitors
from booking a consultation. Do not rewrite the copy.

CONTEXT
State of Fitness is a boutique personal training studio in South Yarra, Melbourne.
It sells small-group and one-to-one training to time-poor professionals aged 30-50.
Training is appointment-only, and consultation availability is the binding
constraint on new members. The studio competes on service quality, not price.
The single job of this page is to get the visitor to book a consultation.

CRITERIA
Assess the copy against these six criteria:
1. Clarity of offer - is it obvious what the studio sells within the first screen?
2. Audience specificity - does the copy speak to a defined person?
3. Differentiation - is there a reason to choose this studio over a chain gym?
4. Objection handling - are cost, intimidation and time hesitations addressed?
5. Call to action - is the next step obvious, singular and low-friction?
6. Proof - is there evidence supporting any claims made?

OUTPUT
Return a table with these columns:
Criterion | Rating (strong / adequate / weak) | What the copy currently does |
Recommended fix | Priority

Rank by priority, where 1 is the change most likely to increase consultation
bookings.

HOMEPAGE COPY
"""
[paste copy]
"""
```

**83.75** — clarity 75 · constraints 85 · structure 80 · verifiability 90 · hallucination 95

**Changed:** Applied RACE — role, task, studio context, table output. Segmented the
prompt into labelled sections. Named six criteria to replace open-ended judgement.
Changed the objective from enquiries to consultation bookings, the actual
constraint on new members. Departed from Prompt Lab's v1 suggested rewrite, which
recommended optimising for SEO — not this page's job.

**What happened:** Structure rose 50 → 80 and constraints 60 → 85, confirming
segmentation and named criteria were the right targets. Verifiability rose 70 → 90
from the criteria alone, without any instruction to cite sources.

Clarity fell 85 → 75. Adding structure cost concision — a longer, sectioned prompt
is less immediately readable than a one-line request, even though it performs
better. Clarity became the weakest dimension and the target for v3.

---

## v3 — constraints and structured output

```
ROLE
Conversion copywriter specialising in boutique fitness studios.

TASK
Audit the homepage copy in the COPY block. Identify what prevents visitors from
booking a consultation. Do not rewrite.

CONTEXT
State of Fitness, South Yarra. Small-group and 1:1 personal training for time-poor
professionals aged 30-50. Appointment-only; consultation availability is the
binding constraint on new members. Competes on service quality, not price.
Page objective: book a consultation.

CRITERIA
clarity_of_offer, audience_specificity, differentiation, objection_handling,
call_to_action, proof

RULES
1. Use only text from the COPY block. Quote the exact line each finding refers to.
2. If a criterion cannot be assessed because the copy is absent, return
   "not_present". Do not infer what the page might say.
3. Flag any health, fitness or body-composition claim made without evidence.
4. Do not invent statistics, testimonials or benchmarks.

OUTPUT
Return one JSON object, nothing else:

{
  "findings": [
    {
      "criterion": "",
      "rating": "strong | adequate | weak | not_present",
      "evidence": "exact quote, or null",
      "issue": "",
      "priority": 1
    }
  ],
  "compliance_flags": [{ "claim": "exact quote", "reason": "" }],
  "summary": "max two sentences"
}

Priority 1 = most likely to increase consultation bookings.

COPY
"""
[paste copy]
"""
```

**87.5** — clarity 80 · constraints 90 · structure 85 · verifiability 90 · hallucination 95

**Changed:** Trimmed prose to recover the clarity lost in v2. Replaced the table
with a fixed JSON schema. Required an exact source quote on every finding, added a
"not_present" fallback, restricted the model to the supplied copy, and added
compliance flags for unsupported health claims.

**What happened:** Clarity recovered 75 → 80, constraints 85 → 90, structure
80 → 85. Verifiability (90) and hallucination (95) did not move despite the
evidence quoting and anti-inference rules — both were already near ceiling after
v2, so the constraint work produced no measurable gain on the dimensions it
targeted.

Improvement is flattening: +10.25 from v1 to v2, +3.75 from v2 to v3. Prompt Lab's
suggested rewrite for v3 was near-identical to the prompt submitted, indicating
structural refinement has reached the limit of what this rubric rewards. This
supports the module's guidance that three to five iterations is the useful range.

---

## v4 — StoryBrand framework and few-shot calibration

```
ROLE
Conversion copywriter specialising in boutique fitness studios. You audit using
Donald Miller's StoryBrand framework (Miller, 2017).

TASK
Audit the homepage copy in the COPY block against the seven StoryBrand elements.
Identify what prevents visitors from booking a consultation. Do not rewrite.

CONTEXT
State of Fitness, South Yarra. Small-group and 1:1 personal training for time-poor
professionals aged 30-50. Appointment-only; consultation availability is the
binding constraint on new members. Competes on service quality, not price.
Page objective: book a consultation.

Brand position: direct honesty about an industry the studio characterises as
running on hype. The copy invites prospects to stay skeptical until the studio has
proven otherwise. Assess whether the page sustains this consistently.

CRITERIA
character - customer as hero, with a stated want
problem - external, internal and philosophical problems named
guide - empathy and authority both demonstrated
plan - a clear, simple process to follow
call_to_action - direct CTA, plus a transitional CTA for those not ready
failure - stakes of not acting made clear
success - transformation stated specifically, not generically

DEPTH REQUIRED (calibration only - do not return these as findings)

element: call_to_action
copy: "We'll come back to you within one business day."
finding: Delivers a promise of delay at the moment intent peaks. For an
appointment-only studio, constrained availability is a positioning asset, but this
phrasing presents it as slowness rather than demand.

element: guide
copy: [pricing behind enquiry form]
finding: The page establishes the studio as a guide by inviting skepticism of the
industry, then withholds pricing behind a form. A prospect attuned to that framing
registers the inconsistency.

RULES
1. Use only text from the COPY block. Quote the exact line each finding refers to.
2. If an element cannot be assessed, return "not_present". Do not infer.
3. Flag any health, fitness or body-composition claim made without evidence.
4. Do not invent statistics, testimonials or benchmarks.

OUTPUT
Return one JSON object, nothing else:

{
  "findings": [
    {
      "element": "",
      "rating": "strong | adequate | weak | not_present",
      "evidence": "exact quote, or null",
      "finding": "",
      "brand_consistency": "consistent | inconsistent | n/a",
      "priority": 1
    }
  ],
  "compliance_flags": [{ "claim": "exact quote", "reason": "" }],
  "summary": "max two sentences on the main barriers to booking a consultation"
}

Priority 1 = most likely to increase consultation bookings.

COPY
"""
[paste copy]
"""
```

**90.25** — clarity 85 · constraints 90 · structure 90 · verifiability 95 · hallucination 95

**Changed:** Replaced the six self-defined criteria with Miller's (2017) StoryBrand
seven-element framework. Added the studio's brand position to the context and a
`brand_consistency` field to detect copy that undermines it. Added two worked
examples to calibrate analytical depth — a shift from zero-shot to few-shot
prompting. Changed the objective from generic conversion to consultation bookings.

**What happened:** Clarity rose 80 → 85 despite v4 being substantially longer than
v3. This contradicts the lesson drawn from v2, where added length appeared to cost
concision. The variable is not length but organisation — v4 adds more content under
clear labels, and the rubric rewards that over brevity.

Verifiability moved 90 → 95, having stalled at 90 in v3 despite the evidence-quoting
rules introduced there. An established framework gives the model concrete elements
to check the copy against, which the self-defined criteria did not.

The reviewer flagged the DEPTH REQUIRED block as confusing, since it is calibration
material rather than output specification. The few-shot examples improved output
depth but cost structural clarity — a real trade-off to resolve in a later version
by relabelling the section and adding an explicit instruction not to return the
examples as findings.

**Correction to the v3 conclusion:** v3 recorded that structural refinement had
reached the limit of what this rubric rewards. v4 gained a further 2.75 points, so
that conclusion was too strong. The flattening trend holds — +10.25, +3.75, +2.75 —
but the ceiling had not been reached. What v3 actually demonstrated is that
*constraint* refinement had plateaued; changing the underlying framework opened a
new dimension of improvement that tightening rules could not.

---

## References

Miller, D. (2017). *Building a StoryBrand: Clarify your message so customers will
listen*. HarperCollins Leadership.
