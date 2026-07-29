# P2 — Hero, headline and CTA rewrite

**Workflow:** Website audit and lead capture
**Chain position:** Step 2 of 3 (generate) — consumes P1 output, feeds P3
**Problem:** Rewrites are done from instinct and drift off brand voice
**Automation potential:** Triggered by P1 findings; produces options for owner selection
**Risks:** Can drift into hype; can invent proof or overstate results
**Human checkpoint:** Owner selects a variant before it goes to P3 for compliance check

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | | | | | | |
| v2 | | | | | | |

---

## v1

```
Write me a better headline, subheading and primary call to action for my gym website homepage. 

Here is the entire homepage supplied as voice reference and context only.

[added entire homepage copy]
```
FINDINGS
**76.25** — clarity 85 · constraints 60 · structure 70 · verifiability 75 · hallucination 90

**What happened:** Constraints scored lowest (60) — no tone, register, length or
proof rules were given.

The output was worse than the copy it replaced. "Personal Training That Fits Your
Life — Not the Other Way Around" is generic fitness marketing; the existing
"You don't need a busier gym. You need a better coach." is sharper and carries the
studio's contrarian position. Without a stated brand register the model defaulted
to a conventional one.

Three further problems the rubric did not detect:
- One variant returned, not three — nothing to choose between
- Conversational preamble and a trailing question, so the output cannot be
  consumed by a downstream step
- No verified proof used, despite the page carrying several usable figures

The prompt scored 76.25 while producing an unusable result. Prompt Lab assesses how
a prompt is constructed, not whether its output serves the business.

---

## v2 — RACE, chain input and brand constraints

```
ROLE
Conversion copywriter specialising in boutique fitness studios.

TASK
Rewrite the hero section, headline and call to action for the State of Fitness
homepage, addressing the findings supplied in the FINDINGS block. Produce three
distinct variants for the owner to choose from.

CONTEXT
State of Fitness, South Yarra. Small-group and 1:1 personal training for time-poor
professionals aged 30-50. Appointment-only; consultation availability is the
binding constraint on new members. Page objective: book a consultation.

Brand position: direct honesty about an industry that runs on hype. The copy
invites prospects to stay skeptical until the studio has proven otherwise. Never
write in a hype register.

Verified proof available for use: 75+ five-star Google reviews, 170+ active
members, 6 exercise-science coaches, 55+ years combined experience, 30-day
money-back guarantee.

RULES
1. Address only findings rated priority 1-3 in the FINDINGS block. Ignore lower
   priorities.
2. Use only the verified proof listed above. Do not invent statistics,
   testimonials or results.
3. Make no health, fitness or body-composition claim of any kind.
4. Limited consultation availability is a positioning asset. Frame it as demand,
   never as delay or slowness.
5. If the FINDINGS block is empty or malformed, return
   {"error": "no valid findings supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "variants": [
    {
      "id": 1,
      "headline": "",
      "subhead": "",
      "cta_primary": "",
      "cta_transitional": "",
      "findings_addressed": [],
      "rationale": "one sentence"
    }
  ]
}
[added entire homepage copy]

FINDINGS
"""
## v2 — RACE, chain input and brand constraints

```
[the v2 prompt]
```

**90.25** — clarity 90 · constraints 95 · structure 85 · verifiability 90 · hallucination 95

**Changed:** Applied RACE with the studio's brand register stated explicitly.
Supplied a closed list of verified proof and prohibited invention beyond it.
Restricted scope to priority 1-3 findings from P1. Added a malformed-input
fallback. Specified JSON output with three variants.

**What happened:** Constraints rose 60 → 95 and verifiability 75 → 90. The verified
proof list did most of that work — naming what may be used is a stronger control
than instructing the model not to invent.

Structure (85) is now the weakest dimension, and the reviewer's fix is formatting
rather than logic: bullet the context block, define the FINDINGS format explicitly,
distinguish headline from subhead. Two of those are worth taking into v3.

**Note on the score plateau:** v2 lands on 90.25, identical to P1 v4. Both prompts
reached the same ceiling by different routes — P1 through an external framework,
P2 through closed-list constraints. This is consistent across the library: the
rubric appears to cap around 90 for well-formed prompts, and further gains come
from formatting rather than substance.
"""
```
---
## v3 — register anchor and field definitions

```
ROLE
Conversion copywriter specialising in boutique fitness studios.

TASK
Rewrite the hero section for the State of Fitness homepage. Produce three
distinct variants addressing the findings in the FINDINGS block.

CONTEXT
- Studio: State of Fitness, South Yarra
- Offer: small-group and 1:1 personal training
- Audience: time-poor professionals aged 30-50
- Model: appointment-only; consultation availability is the binding constraint
- Page objective: book a consultation
- Register: direct, plain, sceptical of an industry that runs on hype. Never
  aspirational or motivational.

REGISTER ANCHOR
The current hero reads: "You don't need a busier gym. You need a better coach."
Match this level of directness. Reject any variant that could plausibly appear on
a chain gym's website.

VERIFIED PROOF (use only these; invent nothing)
- 75+ five-star Google reviews
- 170+ active members
- 6 exercise-science coaches
- 55+ years combined experience
- 30-day money-back guarantee

FIELD DEFINITIONS
- headline: under 10 words, the primary claim
- subhead: one sentence, qualifies who it is for and what makes it different
- cta_primary: the direct action for a ready visitor
- cta_transitional: a lower-commitment step for a visitor not yet ready

RULES
1. Address only findings with priority 1-3. Ignore the rest.
2. Make no health, fitness or body-composition claim.
3. Frame limited availability as demand, never as delay.
4. The three variants must differ in angle, not just wording.
5. Return the JSON object only. No preamble, commentary or trailing questions.
6. If FINDINGS is empty or malformed, return {"error": "no valid findings supplied"}
   and stop.

FINDINGS FORMAT
A JSON object from P1 containing a "findings" array. Each entry has: element,
rating, evidence, finding, brand_consistency, priority.

OUTPUT
{
  "variants": [
    {
      "id": 1,
      "headline": "",
      "subhead": "",
      "cta_primary": "",
      "cta_transitional": "",
      "findings_addressed": [],
      "rationale": "one sentence"
    }
  ]
}
[added entire homepage copy]

FINDINGS
"""
```

**91.5** — clarity 90 · constraints 95 · structure 90 · verifiability 90 · hallucination 95

**Changed:** Converted the context block to bullets. Added a register anchor
quoting the existing hero line and instructing rejection of any variant that could
appear on a chain gym's site. Defined each output field explicitly. Required the
three variants to differ in angle rather than wording. Added an explicit
no-preamble rule. Documented the expected FINDINGS input format.

**What happened:** Structure rose 85 → 90 and clarity held at 90 despite the prompt
growing longer — confirming the v4 finding from P1 that organisation, not length,
drives the clarity score.

91.5 exceeds the ~90 ceiling reached by P1 v4 and P2 v2, so the plateau noted in
those entries was not a hard limit. The gain came from formatting and definitional
precision rather than new constraints, which suggests refinement above 90 is
available but at a low return per edit.

The reviewer's remaining suggestions were a worked FINDINGS example and requiring
proof points to be cited in the rationale. Both are marginal and were not pursued
— P2 stops at three versions, consistent with the improvement curve documented
across the library.
