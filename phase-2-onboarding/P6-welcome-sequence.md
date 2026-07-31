# P6 — Welcome sequence and first-session prep

**Workflow:** New member and client onboarding
**Chain position:** Consumes P5 profile output
**Problem:** Welcome messages are written from scratch each time, so tone varies by coach and intake detail gets missed
**Automation potential:** Triggered on profile creation; drafts the message for coach approval
**Risks:** Can repeat health disclosures back to the client in writing; can prescribe programming before assessment
**Human checkpoint:** Coach edits and sends. Nothing auto-sends.

All examples in this file are synthetic.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | 90 | 75 | 80 | 85 | 90 | 84.25 |
| v2 | 85 | 90 | 80 | 85 | 90 | 85.25 |

---

## v1

```
Write a welcome message to a new member joining my personal training studio, and
tell them what to expect at their first session.

[P5 v2 JSON output]
```

**84.25** — clarity 90 · constraints 75 · structure 80 · verifiability 85 · hallucination 90

**What happened:** The message repeated Jordan's health disclosures back to him in
writing — "especially your dodgy right knee and sleep challenges" — in an email
that may go to a work inbox he does not control. Disclosed on a private intake
form, restated in outbound correspondence, with no coach involved.

It then prescribed a program: "low-impact cardio, strength training, and mobility
work", committed to "protect your knee while building strength", and promised the
session would "support better sleep quality". None of that appears in the profile.
It is programming and an outcome claim about a medical complaint, issued before any
assessment and before a coach has seen the client.

It also resolved "usually in by 7" as "7 AM" — the same silent disambiguation P5 v1
made and P5 v2 avoided. The ambiguity propagated because P5's
`missing_information` returned empty, so the uncertainty was never carried forward.
This demonstrates the cost of an unflagged gap in a chained workflow: an error
suppressed at one step reappears downstream.

Register was wrong throughout: "We're excited", "Looking forward to", exclamation
marks, "making fitness a manageable, enjoyable part of your routine".

**Note on the rubric's suggested rewrite:** Prompt Lab recommended the prompt
"mention how the training will be tailored to Jordan's knee and sleep issues" —
instructing the model to do the thing that constitutes the privacy exposure. The
review optimises for personalisation and has no concept of which client details
should not be echoed back.

This is the strongest instance in the library of a well-formed prompt producing an
output with real exposure, and of the evaluation tool actively recommending the
failure.

---

## v2 — register, disclosure handling and scope limits

```
ROLE
Client services coordinator at a boutique personal training studio.

TASK
Draft a welcome message for the client in the PROFILE block, covering what happens
at their first session. The message goes to a coach for review before sending.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training for professionals
aged 30-50.

Register: direct, plain, no hype. Never motivational, aspirational or
congratulatory. Write as the studio, not as an individual coach.

First session format: 45-60 minutes. Movement assessment, training history
discussion, goal setting. The client trains lightly but does not complete a full
program. Wear normal training clothes, bring water.

RULES
1. Reference only what appears in the PROFILE block. Do not infer or add detail.
2. Do not repeat injury, medical or sleep disclosures back to the client. The coach
   will raise these in person.
3. Acknowledge the client's stated goals in their own framing. Do not restate them
   as outcomes the studio will deliver.
4. Do not describe or prescribe any exercise, program or training approach.
5. Make no claim about results, timeframes or what training will achieve.
6. Do not state prices, session times, coach names or availability.
7. Under 150 words. No exclamation marks.
8. If the PROFILE block is empty or malformed, return
   {"error": "no profile supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "subject_line": "",
  "message_body": "",
  "coach_note": "one sentence flagging anything the coach should adjust or raise",
  "profile_fields_used": []
}

PROFILE
"""
{ "first_name": "Jordan", "age": 38, "occupation_context": "Works in finance, long hours, usually in by 7", "training_history": "Trained a bit at uni, nothing serious since", "stated_goals": [ "lose some weight", "feel less wrecked by Friday", "be able to keep up with my kids" ], "disclosures": [ { "type": "injury", "client_words": "dodgy right knee - did something to it playing footy about ten years ago", "practitioner_involved": "sees a physio occasionally" }, { "type": "sleep", "client_words": "sleep is poor, maybe 5-6 hours", "practitioner_involved": "" } ], "availability": [ "Tues mornings", "Thurs mornings", "sometimes Saturday" ], "coach_flags": [ "dodgy right knee with physio involvement", "poor sleep of 5-6 hours" ], "missing_information": [] }
"""
```

**85.25** — clarity 85 · constraints 90 · structure 80 · verifiability 85 · hallucination 90
**General Review (output audit):** 81.5

**Changed:** Added role, register and first-session detail. Rule 2 prohibits
repeating injury, medical or sleep disclosures back to the client. Rule 4 prohibits
describing or prescribing any exercise or training approach. Added word limit, JSON
output, and a `profile_fields_used` field for auditability.

Run on the same profile as v1 so the prompt was the only variable.

**What happened:** Every substantive v1 failure was corrected. No health disclosures
appear in the message body — both were routed to `coach_note` for the coach to
raise in person. No programming, no outcome claims, no prescribed exercise types.
The "usually in by 7" ambiguity did not propagate, because `occupation_context` was
not used at all.

`profile_fields_used` returned only `first_name`, `training_history` and
`stated_goals`, providing a verifiable record that the disclosure fields were never
drawn on.

Structure scored lowest (80). Both reviews independently flagged the same cause —
the PROFILE data block is not clearly enough separated from the instructions. The
same criticism appeared in P3 and P4, so it is a consistent weakness of this prompt
format rather than a fault specific to P6.

The remaining substantive issue is that goals were paraphrased rather than quoted.
Rule 3 said "in their own framing", which was too loose; "verbatim" would have been
enforceable.

**Method note:** this prompt was scored on both tabs. Prompt Review (85.25) assesses
construction; General Review (81.5) assesses the output against the prompt's own
rules, functioning as a compliance audit. Only the second confirmed that rule 2
actually fired. The gap running through this library — that a prompt score cannot
tell you whether a rule worked — is addressable by running both.

**Reviewer feedback not adopted:** the General Review recommended removing the
greeting to maintain a neutral studio voice. This was rejected. The register rule
targets motivational and aspirational language, not ordinary courtesy, and an
unaddressed welcome email would read as automated. The paraphrasing criticism was
accepted as valid.
