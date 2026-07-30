# P4 — Inbound enquiry triage and response draft

**Workflow:** Website audit and lead capture
**Chain position:** Standalone
**Problem:** Enquiries arrive at all hours and sit unanswered until someone is free; speed of first response drives booking rates
**Automation potential:** Triggered on form submission; classifies the enquiry and drafts a reply for coach approval
**Risks:** Can invent pricing or availability; can mishandle injury or medical disclosures
**Human checkpoint:** Coach reviews and sends. Nothing auto-sends.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | | | | | | |
| v2 | | | | | | |

## v1

```
Write a reply to this enquiry from someone interested in personal training at my studio.

"Hi, I'm 42 and haven't trained in about 5 years. I've got an ongoing lower back
issue from an old injury. Wondering what your prices are and whether you'd be able
to work around it. Thanks, Sam"
```

**77.5** — clarity 90 · constraints 70 · structure 75 · verifiability 60 · hallucination 80

**What happened:** Verifiability scored lowest (60) — the reply invented a pricing
structure ("priced at [insert price] per session, with package options available")
that does not exist as described, and signed off with placeholder fields a coach
could easily send unedited.

More seriously, it wrote in first person as a coach and asserted clinical
competence: "I have experience tailoring programs to accommodate and strengthen
areas with past injuries safely." That is an unqualified claim about managing an
injury made to a person who has just disclosed one, with no assessment and no
mention of working alongside their treating practitioner. It also promised a
"customised plan that supports your back" — an outcome claim on a medical
complaint.

Register was wrong throughout: exclamation marks, "I'm glad you're considering,"
"Looking forward to hearing from you." Motivational, not direct.

No classification and no structured output, so the enquiry cannot be routed or
prioritised.

The rubric scored hallucination at 80 despite the invented pricing and the clinical
claim, and did not register the injury disclosure as a risk at all. This is the
clearest instance in the library of the review scoring construction while missing
the business and legal exposure in the output.

## v2 — classification, disclosure handling and structured output

```
ROLE
Client services coordinator at a boutique personal training studio.

TASK
Classify the enquiry in the ENQUIRY block and draft a reply for coach review.

CONTEXT
State of Fitness, South Yarra. Appointment-only small-group and 1:1 personal
training for professionals aged 30-50. Coaches are exercise-science qualified and
work alongside a client's osteo or physio where relevant.

Register: direct, plain, no hype. Never motivational or aspirational.

The next step is always a free 15-minute call, followed by a consultation and
movement assessment.

RULES
1. Never state prices. If asked, say pricing is covered on the call.
2. Never state specific availability, session times or coach names.
3. If the enquirer mentions an injury, pain or medical condition: acknowledge it,
   state that coaches work alongside their osteo or physio, and make no assessment,
   diagnosis or claim about whether it can be resolved.
4. Make no claim about results, timeframes or outcomes.
5. Under 120 words. No exclamation marks.
6. If the enquiry is spam, a supplier pitch or unrelated to training, classify it
   and return an empty draft.
7. If the ENQUIRY block is empty, return {"error": "no enquiry supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "classification": "new_lead | returning_member | injury_disclosure | price_only | spam | other",
  "priority": "high | medium | low",
  "disclosure_flag": true,
  "questions_asked": [],
  "draft_reply": "",
  "coach_note": "one sentence for the reviewing coach"
}

Priority is high when the enquirer states a specific goal or timeframe, medium for
a general enquiry, low for price-only or unclear.

ENQUIRY
"""
Hi, I'm 42 and haven't trained in about 5 years. I've got an ongoing lower back
issue from an old injury. Wondering what your prices are and whether you'd be able
to work around it. Thanks, Sam
"""
```

**90.5** — clarity 90 · constraints 95 · structure 85 · verifiability 90 · hallucination 95

**Changed:** Added role, studio context and register. Prohibited pricing,
availability and outcome claims. Added rule 3 governing injury disclosures, a
120-word limit, spam handling, and rule 7 for empty input. Specified JSON output
with classification, priority and a disclosure flag.

Run on the same enquiry as v1 so the prompt was the only variable.

**What happened:** Every v1 failure was corrected. No invented pricing, no clinical
competence claim, correct register, and the injury correctly classified with
`disclosure_flag: true` and priority high. The reply exceeded the rule by stating
the studio does not give injury advice by email — a safer position than rule 3
required.

Two gaps remain, both in fields the rules under-specified rather than in the safety
rules:

- Priority was returned as high, but Sam states neither a goal nor a timeframe. By
  the stated definition this is medium. The model appears to have weighted the
  injury disclosure instead. The definitions need worked examples, or priority
  needs to be derived from classification rather than judged independently.
- `questions_asked` returned empty although the draft asks the enquirer to confirm
  a call time. The field was never defined, so the model had no basis for
  populating it.

Both were flagged independently by the reviewer, which is the first instance in
this library of the rubric identifying a substantive gap rather than a formatting
one.

**On the v1 comparison:** v1 scored 77.5 while asserting clinical competence to a
person disclosing a back injury. v2 scored 90.5 with that risk removed. The 13-point
gap understates the difference — the change is between a reply that creates
duty-of-care exposure and one that does not.
