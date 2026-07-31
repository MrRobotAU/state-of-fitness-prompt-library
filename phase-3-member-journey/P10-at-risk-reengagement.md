# P10 — At-risk member identification and re-engagement

**Workflow:** Client journey and recurring member tasks
**Chain position:** Standalone
**Problem:** Attendance drop-off is noticed late, usually when a member cancels. By then the reason has hardened.
**Automation potential:** Runs weekly against attendance data; flags members whose pattern has changed and drafts an outreach message for coach review
**Risks:** Can infer reasons for absence that were never stated; can read as surveillance; can pressure a member who has decided to leave
**Human checkpoint:** Coach reviews the flag and the draft. Coach decides whether to contact at all.

All examples in this file are synthetic.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | | | | | | 82.5 |
| v2 | 90 | 95 | 90 | 85 | 95 | 90.5 |

---

## v1

```
Look at this member's attendance and tell me if they're at risk of leaving. Write
them a message to win them back.

Member: Tom, 51. Member for 14 months. Usual pattern: 2 sessions a week, Tues and
Fri. Last 6 weeks: 3 sessions total, none in the last 12 days. No contact from him.
Last session note: "shortened session, said he was flat out at work."
```

markdown
## v1

```
Look at this member's attendance and tell me if they're at risk of leaving. Write
them a message to win them back.

Member: Tom, 51. Member for 14 months. Usual pattern: 2 sessions a week, Tues and
Fri. Last 6 weeks: 3 sessions total, none in the last 12 days. No contact from him.
Last session note: "shortened session, said he was flat out at work."
```

**82.25** — clarity 85 · constraints 70 · structure 75 · verifiability 90 · hallucination 95

**What happened:** The message disclosed the surveillance. "We've missed seeing you
at the gym lately" tells Tom his attendance is monitored and has been discussed
internally — accurate, but not something a member expects to have raised.

Worse, it repeated a private session note back to him. "I hope work isn't keeping
you too overwhelmed" derives directly from the coach's internal note that he was
flat out at work. A note recorded for the coaching team surfaced in outbound
correspondence.

It inferred a reason for the absence that Tom never gave and offered unauthorised
concessions — "adjust your schedule", "even a shorter workout" — then closed by
offering "follow-up plans or incentives", proposing a commercial response with no
authority.

The framing was guilt-inducing throughout: "we've missed you", "looking forward to
having you back soon". A member who has decided to stop training receives pressure
rather than an exit.

The internal assessment also overreached, concluding Tom "is showing signs of
disengagement" and "might be at risk of leaving" from attendance data alone, with a
speculative cause attached.

**Note on the rubric's suggested rewrite:** Prompt Lab recommended the message
"acknowledges the attendance drop and last note" — instructing the prompt to do the
two things that constitute the exposure. The fifth consecutive instance, after P6,
P7, P8 and P9, of the review prescribing the failure. The pattern is now
unambiguous: the review optimises for personalisation and engagement, and in a
service business those are the same direction as surveillance disclosure and
unauthorised commitment.

---

## v2 — RACE with inference limits and opt-out framing

```
ROLE
Client services coordinator at a boutique personal training studio.

TASK
Assess the attendance record in the MEMBER block against the member's own
established pattern. Draft an outreach message for the coach to review. The coach
decides whether to send it.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training. Members are
professionals with variable workloads; a quiet fortnight is common and is not
evidence of dissatisfaction.

Register: direct, plain, no hype. Never guilt-inducing, urgent or promotional.
The member is not a churn risk to be recovered; they are a person whose schedule
has changed.

RULES
1. Base the assessment only on the stated attendance data. Do not infer the reason
   for absence, the member's feelings, or their intention to leave.
2. Do not reference the number of sessions missed, count absences, or describe the
   pattern back to the member. Internal analysis stays internal.
3. Do not offer discounts, free sessions, pauses or promotions. Any commercial
   response is the owner's decision.
4. Do not ask the member to commit to a return, book a session, or explain
   themselves.
5. Make the message easy to ignore. It must not require a reply.
6. State plainly that pausing or stopping is a legitimate option.
7. Under 80 words. No exclamation marks.
8. If the attendance data is insufficient to establish a baseline pattern, set
   assessment to "insufficient_data" and return an empty draft.
9. If the MEMBER block is empty or malformed, return
   {"error": "no member supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "assessment": "pattern_changed | pattern_stable | insufficient_data",
  "evidence": "the specific data supporting the assessment",
  "inferences_avoided": [],
  "recommend_contact": true,
  "draft_message": "",
  "coach_note": "one sentence on what the coach should weigh before sending"
}

MEMBER
"""
first_name: Tom
age: 51
tenure_months: 14
established_pattern: 2 sessions per week, Tuesday and Friday
last_6_weeks: 3 sessions total
days_since_last_session: 12
member_initiated_contact: none
last_session_note: "shortened session, said he was flat out at work"
"""
```

**90.5** — clarity 90 · constraints 95 · structure 90 · verifiability 85 · hallucination 95

**Changed:** Reframed the task from churn recovery to schedule change. Prohibited
inferring reasons or feelings, describing the attendance pattern back to the
member, offering commercial concessions, and asking the member to commit or
explain. Required the message to be ignorable and to name pausing or stopping as
legitimate. Added an `inferences_avoided` field and an `insufficient_data` fallback.

Run on the same member as v1 so the prompt was the only variable.

**What happened:** Every v1 exposure was removed. No attendance figures, no
reference to the private session note, no inferred reason, no discount, no guilt
framing. The message states pausing or stopping is fine and closes with "no need to
reply unless you want to."

The session note stayed internal, appearing only in `coach_note` where it belongs.
The internal assessment is now evidence-bound: `pattern_changed` supported by the
stated data, with no speculation about cause or intent.

`inferences_avoided` returned "reason for absence", "member's feelings", "member's
intention to leave". This field has no equivalent elsewhere in the library — it
requires the model to declare what it deliberately did not conclude, producing an
auditable record of restraint rather than only a record of output. It is the most
useful single field designed in this project and would be worth retrofitting to P5
and P7.

**Residual issue:** the message avoids *stating* that attendance is monitored, but a
studio that spontaneously offers to let you pause has evidently noticed something.
The surveillance is implied by the fact of contact, not by its wording. Rule 2
controls disclosure but cannot control inference. This is a limit of prompt design:
the remaining risk sits in the decision to send at all, which is why
`recommend_contact` is a recommendation to a coach rather than a trigger.

**On the rubric:** 82.25 → 90.5. The review's suggested rewrite for v1 recommended
acknowledging the attendance drop and the session note — the two failures. Across
P6, P7, P8, P9 and P10 the review prescribed the failure it had just scored as safe.
