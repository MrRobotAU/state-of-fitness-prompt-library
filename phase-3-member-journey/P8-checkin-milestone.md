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
| v1 | | | | | | |
| v2 | | | | | | |

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
Client services coordinator at a boutique personal training studio.

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

Scores:
Changed:
What happened:
