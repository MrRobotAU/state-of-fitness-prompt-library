# P8 — Scheduled check-in and milestone messages

**Workflow:** Client journey and recurring member tasks
**Chain position:** Standalone
**Problem:** Check-ins happen when a coach remembers, so quieter members get fewer; milestones pass unmarked
**Automation potential:** Triggered on tenure or session count; drafts a message for coach approval
**Risks:** Can imply progress the studio has not measured; can read as automated to a member who knows their coach
**Human checkpoint:** Coach edits and sends. Nothing auto-sends.

All examples in this file are synthetic.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | 85 | 70 | 75 | 80 | 90 | 80.25 |
| v2 | 90 | 95 | 85 | 90 | 95 | 90.25 |

---

## v1

```
Write a check-in message for a member who has been training at my studio for six months.

Member: Priya, 44. Trains Mon and Wed mornings. Has been a member 6 months.
Attendance has been consistent. Original goals were to get stronger and manage a
recurring shoulder issue.
```
**What happened:** The message raised Priya's shoulder in writing — "I hope your
shoulder is managing well" — the same disclosure failure as P6 v1. A recurring
medical issue referenced in outbound correspondence with no coach involved.

It asserted progress the studio has not measured: "I hope you're feeling stronger",
"keep making progress". Neither is evidenced by anything in the input, which records
only tenure and attendance.

It congratulated the member on a tenure milestone — "how impressed I am with your
consistent dedication", "real commitment", "keep up the great work" — treating the
passage of six months as an achievement the studio is entitled to praise.

Register was wrong throughout: motivational, exclamation marks, first-person coach
voice with a placeholder signature.

**Note on the rubric's suggested rewrite:** Prompt Lab recommended making the
message "friendly and motivational" and instructing it to "acknowledge her
progress". Both are the failures documented above. As in P6, the review does not
merely miss the problem — it prescribes it, because it optimises for engagement and
personalisation with no concept of what a business is entitled to claim.

This is now consistent across P4, P6, P7 and P8: the review's suggested rewrites
reliably push toward warmth and specificity, which in a health and fitness context
is the direction of both clinical overreach and privacy exposure.

---

## v2 — RACE with trigger types and evidence limits

```
ROLE
Client services coordinator at a boutique personal training studio called State of Fitness in South Yarra. 

TASK
Draft a check-in message for the member in the MEMBER block. The trigger type is
stated in the block. The message goes to the member's coach for review before
sending.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training. Members train
with a coaching team who know them by name, so a message that reads as automated
undermines the relationship it is meant to support.

Register: direct, plain, no hype. Never congratulatory, motivational or
celebratory. Write as the studio.

RULES
1. Reference only what appears in the MEMBER block. Do not infer progress,
   improvement or how the member feels.
2. Make no claim about results or physical change. The studio has not measured it.
3. Do not reference injuries, pain or medical matters. The coach raises these in
   person.
4. Do not prescribe exercises, programming or session frequency.
5. A tenure milestone is a fact, not an achievement. State it plainly and do not
   congratulate.
6. Close with one open question the member can answer in a sentence.
7. Under 100 words. No exclamation marks.
8. If the MEMBER block is empty or malformed, return
   {"error": "no member supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "trigger": "",
  "message_body": "",
  "coach_note": "one sentence flagging anything the coach should adjust",
  "fields_used": []
}

MEMBER
"""
first_name: Priya
age: 44
tenure_months: 6
trigger: six_month_tenure
attendance_pattern: consistent, Mon and Wed mornings
original_goals: ["get stronger", "manage a recurring shoulder issue"]
"""
```

**90.25** — clarity 90 · constraints 95 · structure 85 · verifiability 90 · hallucination 95

**Changed:** Added role, register and the reason the register matters. Prohibited
inferring progress or feelings, referencing injuries, prescribing programming, and
congratulating on tenure. Required one open question and a 100-word cap. Added JSON
output with a `fields_used` audit trail.

Run on the same member as v1 so the prompt was the only variable.

**What happened:** Every v1 failure was corrected. The shoulder is absent. No claim
of progress, no congratulation, no exclamation marks. Tenure is stated as fact:
"you have now been with State of Fitness for six months."

`fields_used` returned `first_name`, `tenure_months` and `attendance_pattern`,
confirming `original_goals` — which contains the medical disclosure — was never
drawn on. The same auditability mechanism used in P6.

Two observations:

- At 38 words the message is well under the cap and arguably too spare. Stripping
  every unverifiable claim leaves little content, which is the honest consequence of
  the constraint rather than a fault: the studio has not measured Priya's progress,
  so it has nothing to say about it. The open question carries the message.
- `coach_note` described the prompt's own compliance — "avoids any mention of
  shoulder issues... as per guidelines" — rather than flagging anything actionable
  for the coach. The field was defined loosely and produced a self-report instead of
  a note. A stricter definition, or `null` when nothing needs raising, would fix it.

Structure (85) was again the lowest dimension, with the reviewer citing section
separation. Consistent with P3, P4 and P6, confirming this as a property of the
prompt format rather than any individual prompt.
