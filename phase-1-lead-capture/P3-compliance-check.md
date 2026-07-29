# P3 — Brand voice and claims compliance check

## v1

```
Check this website copy for anything that sounds off-brand or makes claims we can't back up.

Homepage content for State of Fitness:
[full homepage copy pasted]
```

**80.25** — clarity 85 · constraints 70 · structure 75 · verifiability 80 · hallucination 90

**What happened:** "Off-brand" was undefined, so the model applied a generic
marketing standard and recommended softening the studio's skeptical positioning —
the paragraph that differentiates it. It also proposed rewording a verbatim Google
review, which would fabricate a testimonial.

Real issues were found (six-month change claim, healthspan claim, guarantee terms)
but mixed into tone preferences with no severity ranking. No structured output, so
nothing can gate publishing.

Wrong input: the full homepage was supplied instead of P2's variants, so the chain
was not exercised. Corrected in v2.

Scored 80.25 while producing advice that would have damaged the brand — the rubric
assesses construction, not usefulness.

---

ROLE
Brand and compliance reviewer for a boutique personal training studio.

TASK
Review each copy variant in the DRAFTS block. Determine whether it matches the
studio's brand register and whether any claim requires substantiation before
publication. Do not rewrite.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training for
professionals aged 30-50.

Brand register: direct, plain, sceptical of an industry that runs on hype. Never
aspirational, motivational or transformational in tone.

Register anchor: "You don't need a busier gym. You need a better coach."

VERIFIED PROOF (the only substantiated claims available)
- 75+ five-star Google reviews
- 170+ active members
- 6 exercise-science coaches
- 55+ years combined experience
- 30-day money-back guarantee

RULES
1. Flag any factual claim not covered by the verified proof list.
2. Flag any health, fitness, weight-loss or body-composition claim. These require
   substantiation under Australian Consumer Law and must not publish unreviewed.
3. Flag any language that reads as hype, guarantees an outcome, or implies a
   typical result.
4. The brand register is a deliberate commercial position, not a defect. Do not
   flag directness, bluntness or criticism of the fitness industry as off-brand.
5. Never suggest altering a quoted testimonial or review. Flag one only if the
   quoted claim itself requires substantiation.
6. Quote the exact text triggering each flag.
7. Do not rewrite or suggest replacement copy.

OUTPUT
Return one JSON object, nothing else:

{
  "reviews": [
    {
      "variant_id": 1,
      "register_match": "pass | fail",
      "register_note": "one sentence",
      "flags": [
        { "text": "exact quote", "type": "unsupported_claim | health_claim | hype", "reason": "" }
      ],
      "verdict": "approved | revise | blocked"
    }
  ]
}

Verdict rules: any health_claim flag returns "blocked". Any unsupported_claim or
register failure returns "revise". Otherwise "approved".


"""

"""
