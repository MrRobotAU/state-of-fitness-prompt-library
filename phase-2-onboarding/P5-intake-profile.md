# P5 — Intake form to structured client profile

**Workflow:** New member and client onboarding
**Chain position:** Standalone — output feeds P6 and P7
**Problem:** Intake forms arrive as free text and are read from scratch by whichever coach takes the session; details get missed and nothing is comparable between clients
**Automation potential:** Triggered on form submission; produces a consistent profile record for the coaching team
**Risks:** Processes health information under the Privacy Act 1988 (Cth); can infer clinical detail that was never disclosed
**Human checkpoint:** Coach verifies the profile against the raw form before the first session

All examples in this file are synthetic. No real client data is used.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | | | | | | |
| v2 | | | | | | |

---

## v1

```
Summarise this new client intake form into a profile for the coaching team.

"Name: Jordan. Age 38. Works in finance, long hours, usually in by 7. Trained a
bit at uni, nothing serious since. Wants to lose some weight and feel less wrecked
by Friday. Has a dodgy right knee - did something to it playing footy about ten
years ago, sees a physio occasionally. Also mentioned sleep is poor, maybe 5-6
hours. Available Tues and Thurs mornings, sometimes Saturday. Goal: 'be able to
keep up with my kids'."
```

**79.25** — clarity 85 · constraints 60 · structure 70 · verifiability 90 · hallucination 95

**What happened:** Constraints scored lowest (60) — no format, no schema, no rules
on inference.

The output was more restrained than P4 v1. It stayed close to Jordan's own words,
kept "dodgy right knee" rather than clinicalising it, and recommended no
programming. Unlike P4, no fabrication occurred. The intake was already
well-structured, which left little room to invent.

Three defects remain:

- "usually in by 7" was rendered as "usually starts by 7 AM". The original is
  ambiguous — 7am start or 7pm finish — and the model resolved it silently rather
  than flagging it. For a client whose availability determines scheduling, a
  quietly guessed detail is worse than a gap.
- Prose bullets under free-text headings. Nothing is comparable between clients and
  no downstream step can consume it, so the automation claim fails.
- No null handling and no record of what is missing. Jordan gave no injury detail
  beyond "did something to it", no current training, no timeframe. A coach reading
  this cannot distinguish "not asked" from "not applicable".

The rubric scored verifiability 90 and hallucination 95, neither of which caught
the silent disambiguation of "in by 7". The review compares output to input for
contradiction, not for inference filling a gap.

**Note on method:** unlike P2 and P4, v1 here did not fail badly. This is worth
recording rather than overstating — a well-structured input limits how much a weak
prompt can go wrong. The case for v2 rests on consistency and machine-readability,
not on repairing a bad output.

## v2 — schema, disclosure boundaries and ambiguity handling

```
ROLE
Client onboarding coordinator at a boutique personal training studio.

TASK
Convert the intake form in the INTAKE block into a structured client profile for
the coaching team.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training for professionals
aged 30-50. Profiles are read by a coach before the first session and must be
accurate, comparable between clients, and free of anything the client did not say.

RULES
1. Record only what the client stated. Do not infer, diagnose or expand.
2. If a field is not covered in the intake, return null. Do not guess.
3. Record injuries and medical mentions verbatim in the client's own words. Make
   no assessment of cause, severity or whether training can address them.
4. Do not recommend exercises, programming, loads or contraindications.
5. Do not record identifying detail beyond first name and age.
6. If a statement is ambiguous, record it verbatim and add it to
   missing_information rather than resolving it.
7. If the INTAKE block is empty, return {"error": "no intake supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "first_name": "",
  "age": null,
  "occupation_context": "",
  "training_history": "",
  "stated_goals": [],
  "disclosures": [
    { "type": "injury | medical | sleep | other", "client_words": "", "practitioner_involved": "" }
  ],
  "availability": [],
  "coach_flags": [],
  "missing_information": []
}

coach_flags: matters requiring coach attention before session one, stated neutrally.
missing_information: fields a coach should confirm at the consultation, including
anything ambiguous in the intake.

INTAKE
"""
Name: Jordan. Age 38. Works in finance, long hours, usually in by 7. Trained a
bit at uni, nothing serious since. Wants to lose some weight and feel less wrecked
by Friday. Has a dodgy right knee - did something to it playing footy about ten
years ago, sees a physio occasionally. Also mentioned sleep is poor, maybe 5-6
hours. Available Tues and Thurs mornings, sometimes Saturday. Goal: 'be able to
keep up with my kids'.
"""
```

**93.5** — clarity 95 · constraints 90 · structure 90 · verifiability 95 · hallucination 95

**Changed:** Added role, context and a fixed JSON schema. Prohibited inference,
diagnosis and programming advice. Required injuries recorded verbatim with the
treating practitioner captured separately. Limited identifying detail to first name
and age. Added rule 6 requiring ambiguous statements to be recorded verbatim and
listed in `missing_information`, and rule 7 for empty input.

Run on the same intake as v1 so the prompt was the only variable.

**What happened:** The disclosure handling worked exactly as intended. "Dodgy right
knee" was preserved verbatim, the physio captured in its own field, sleep recorded
separately as a disclosure rather than folded into general context. No clinical
language, no programming advice, no inference about the knee.

Rule 6 half-worked. "Usually in by 7" was recorded verbatim rather than resolved to
"7 AM" as v1 did — so the first half of the rule held. But `missing_information`
returned empty, so the ambiguity was never surfaced to the coach. The rule
successfully prevented a wrong answer and failed to flag that a question remains.

That is the more subtle failure of the two. v1 gave a coach a confident wrong time;
v2 gives a coach an accurate but unusable one with nothing indicating it needs
confirming. Neither profile lets a coach schedule Jordan correctly.

The likely cause is that rule 6 asks the model to detect ambiguity without defining
it. `coach_flags` was populated correctly because it maps to visible content;
`missing_information` requires recognising an absence, which is a harder judgement
and was given no criteria.

**Fix, if a v3 were run:** replace open-ended ambiguity detection with a named
checklist — confirm training availability, current activity level, injury detail,
timeframe — so the model checks against a list rather than exercising judgement.
This mirrors the P1 finding that named criteria outperform open-ended assessment.

**On the score:** 93.5 is the highest recorded in this library, achieved while a
rule intended to protect scheduling accuracy silently half-failed. Consistent with
P4, where a safety rule gap sat behind a 90.25. The review assesses whether a
prompt is well-formed, not whether its rules actually fire.

---

## Data handling

This prompt processes health information, which is sensitive information under the
Privacy Act 1988 (Cth). Three controls apply:

- Rule 5 limits identifying detail to first name and age
- Rules 1 and 3 prevent the model inferring or characterising clinical detail
- A coach verifies the profile against the raw intake before the first session

All examples in this repository are synthetic. No real client intake data is used
or stored here.
