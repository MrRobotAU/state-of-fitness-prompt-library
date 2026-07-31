# P7 — Goal-setting brief

**Workflow:** New member and client onboarding
**Chain position:** Consumes P5 profile output
**Problem:** Stated goals are vague and unmeasured, so there is no baseline to review against at three or six months
**Automation potential:** Triggered after the consultation; drafts a brief for coach completion
**Risks:** Can set goals the client did not state; can define success in body-composition terms not raised by the client
**Human checkpoint:** Coach completes and agrees the brief with the client in person

All examples in this file are synthetic.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | | | | | | |
| v2 | | | | | | |

---

## v1

```
Turn this client profile into a goal-setting brief with measurable targets.

{ "first_name": "Jordan", "age": 38, "occupation_context": "Works in finance, long hours, usually in by 7", "training_history": "Trained a bit at uni, nothing serious since", "stated_goals": [ "lose some weight", "feel less wrecked by Friday", "be able to keep up with my kids" ], "disclosures": [ { "type": "injury", "client_words": "dodgy right knee - did something to it playing footy about ten years ago", "practitioner_involved": "sees a physio occasionally" }, { "type": "sleep", "client_words": "sleep is poor, maybe 5-6 hours", "practitioner_involved": "" } ], "availability": [ "Tues mornings", "Thurs mornings", "sometimes Saturday" ], "coach_flags": [ "dodgy right knee with physio involvement", "poor sleep of 5-6 hours" ], "missing_information": [] }
```

**79.5** — clarity 85 · constraints 70 · structure 75 · verifiability 80 · hallucination 85

**What happened:** The output stopped being a brief and became a clinical plan.

It set a numeric weight-loss target — 5-7% over 12 weeks — with a worked example
against a body weight Jordan never disclosed. It prescribed weekly weigh-ins and
monthly progress photos, neither raised by the client, both carrying real risk with
a client whose relationship to body image is unknown.

It prescribed programming throughout: low-impact cardio, knee-stability work,
"adjust nutrition guidance if applicable" — nutrition advice from a training studio,
proposed before any assessment.

Most seriously, it recommended "possible referral for sleep specialist if no
improvement by week 6". Sleep was recorded as a passing disclosure, not a goal. The
model converted it into a treatment target and proposed a clinical referral pathway,
which is outside the scope of coaching entirely.

It also treated the knee injury as a managed condition with flare-up monitoring, and
repeated the "in by 7" disambiguation error seen in P5 v1 and P6 v1.

The rubric scored hallucination 85 and verifiability 80 while the output invented a
body weight, a target, a testing protocol, a nutrition scope and a medical referral.
Every number in the brief was fabricated. This is the third consecutive prompt where
the review rated an output safe that was not, and the widest gap between score and
exposure recorded in this library.

---

## v2 — RACE with scope limits and client-owned targets

```
ROLE
Client onboarding coordinator at a boutique personal training studio.

TASK
Convert the profile in the PROFILE block into a goal-setting brief. The brief is
completed by a coach with the client during the consultation. It is a working
document, not a plan.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training for professionals
aged 30-50. The brief exists so progress can be reviewed against something the
client agreed to, rather than against an assumption.

RULES
1. Use only goals the client stated. Do not add, merge or reinterpret them.
2. Do not set numeric targets. Propose how each goal could be measured and leave
   the target for the client and coach to agree.
3. Do not propose weight, body-fat or body-composition measures unless the client
   raised that goal themselves.
4. Do not prescribe exercises, programming, session frequency or nutrition.
5. Make no claim about timeframes or achievability.
6. For each goal, note what would need to be established at the consultation before
   progress can be tracked.
7. If the PROFILE block is empty or malformed, return
   {"error": "no profile supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "client_first_name": "",
  "goals": [
    {
      "client_words": "",
      "possible_measures": [],
      "baseline_required": "",
      "review_point": "3 months | 6 months | to be agreed"
    }
  ],
  "constraints_noted": [],
  "coach_actions": [],
  "out_of_scope": []
}

constraints_noted: availability or scheduling factors stated in the profile.
coach_actions: what the coach must confirm or agree at the consultation.
out_of_scope: anything in the profile that is not a training goal and should be
handled elsewhere.

PROFILE
"""
{ "first_name": "Jordan", "age": 38, "occupation_context": "Works in finance, long hours, usually in by 7", "training_history": "Trained a bit at uni, nothing serious since", "stated_goals": [ "lose some weight", "feel less wrecked by Friday", "be able to keep up with my kids" ], "disclosures": [ { "type": "injury", "client_words": "dodgy right knee - did something to it playing footy about ten years ago", "practitioner_involved": "sees a physio occasionally" }, { "type": "sleep", "client_words": "sleep is poor, maybe 5-6 hours", "practitioner_involved": "" } ], "availability": [ "Tues mornings", "Thurs mornings", "sometimes Saturday" ], "coach_flags": [ "dodgy right knee with physio involvement", "poor sleep of 5-6 hours" ], "missing_information": [] }
"""
```

**90.25** — clarity 90 · constraints 95 · structure 90 · verifiability 85 · hallucination 95

**Changed:** Restricted goals to the client's stated wording. Prohibited numeric
targets, deferring them to the client and coach. Prohibited exercise, programming,
frequency and nutrition. Required a baseline to be established for each goal, and
added an `out_of_scope` field for profile content that is not a training goal.

Run on the same profile as v1 so the prompt was the only variable.

**What happened:** Every v1 fabrication was eliminated. No invented body weight, no
percentage target, no 12-week timeframe, no nutrition scope. Goals were quoted
verbatim rather than reworded into fitness language.

The most significant correction is sleep. v1 converted a passing disclosure into a
treatment target with a specialist referral pathway; v2 placed it in `out_of_scope`
as "sleep issues beyond noting as constraint" — the correct boundary for a coaching
service.

Two defects remain:

- `review_point` returned the literal schema string "3 months | 6 months | to be
  agreed" for all three goals rather than selecting one. The model copied the enum
  placeholder instead of resolving it. Format specification alone was insufficient;
  the field needed an instruction to choose.
- Sleep appears in three places — `constraints_noted`, `coach_actions` and
  `out_of_scope` — with different handling in each. `coach_actions` includes
  "discuss impact of poor sleep on training and recovery", which sits close to the
  boundary `out_of_scope` was meant to establish. The reviewer independently flagged
  the overlap between these fields.

Rule 3 worked as designed but is worth noting: "lose some weight" is a stated goal,
so body-composition measures were legitimate here. The model proposed progress
photos among them — permitted by the rule, but the measure carrying most risk for a
client whose relationship to body image is unknown. A stricter version would exclude
photographic measures unless the client requests them.

**On the pattern:** v1 scored 79.5 while fabricating a body weight, a target, a
testing protocol and a medical referral. v2 scored 90.25 with all of it removed. As
with P4 and P6, the score gap understates the difference — the change is between an
output that creates clinical scope exposure and one that does not.
