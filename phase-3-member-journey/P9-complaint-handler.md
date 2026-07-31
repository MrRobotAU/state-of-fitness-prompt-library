# P9 — Enquiry, complaint and cancellation handler

**Workflow:** Client journey and recurring member tasks
**Chain position:** Standalone
**Problem:** Complaints and cancellations arrive at unpredictable times and are answered inconsistently depending on who sees them first
**Automation potential:** Triggered on inbound member message; classifies, routes and drafts a reply for owner or coach review
**Risks:** Can concede or refuse a refund without authority; can respond to a distressed member without a person reading it
**Human checkpoint:** Owner reviews and sends. Nothing auto-sends. Escalations go to the owner, not a coach.

All examples in this file are synthetic.

| Version | Clarity | Constraints | Structure | Verifiability | Hallucination | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| v1 | | | | | | |
| v2 | | | | | | |

---

## v1

```
Write a reply to this member who wants to cancel their membership.

"Hi, I want to cancel my membership. Honestly the last few months haven't been
worth what I'm paying. My coach changed twice without anyone telling me and the
last session felt like they hadn't read my program at all. I'd like a refund for
the last month. Marcus"
```

Scores:
What happened:

---

## v2 — RACE with authority limits and escalation

```
ROLE
Client services coordinator at a boutique personal training studio.

TASK
Classify the member message in the MESSAGE block, determine whether it must be
escalated, and draft a reply for review. You do not have authority to resolve
anything.

CONTEXT
State of Fitness, South Yarra. Appointment-only personal training. Members pay
monthly. A 30-day money-back guarantee exists but its terms are not supplied to
you.

Register: direct, plain, no hype. Never defensive, never effusively apologetic.
Acknowledge what the member said without conceding or disputing it.

RULES
1. Never accept, refuse or discuss a refund, credit, discount or contract variation.
   State that the owner will respond directly.
2. Never dispute, explain away or contextualise a service complaint. Acknowledge it
   as stated.
3. Never blame or name a coach, or comment on a coach's conduct.
4. Make no commitment about what the studio will do, beyond that someone will
   respond and by when.
5. If the message mentions injury, illness, distress or a safety concern, set
   escalate to "owner_urgent" and keep the draft to acknowledgement only.
6. Do not attempt to retain the member or ask them to reconsider.
7. Under 120 words. No exclamation marks.
8. If the MESSAGE block is empty or malformed, return
   {"error": "no message supplied"} and stop.

OUTPUT
Return one JSON object, nothing else:

{
  "classification": "cancellation | complaint | billing | enquiry | mixed | other",
  "issues_raised": [],
  "financial_request": true,
  "escalate": "owner | owner_urgent | coach | none",
  "draft_reply": "",
  "owner_note": "one sentence on what the owner needs to decide"
}

MESSAGE
"""
Hi, I want to cancel my membership. Honestly the last few months haven't been
worth what I'm paying. My coach changed twice without anyone telling me and the
last session felt like they hadn't read my program at all. I'd like a refund for
the last month. Marcus
"""
```

**90.25** — clarity 90 · constraints 95 · structure 90 · verifiability 85 · hallucination 95

**Changed:** Stated explicitly that the role holds no authority to resolve anything.
Prohibited discussing refunds, disputing complaints, naming coaches, committing to
actions, and attempting retention. Added an urgent escalation path for injury,
distress or safety. Specified JSON output with classification, escalation routing
and an owner decision note.

Run on the same message as v1 so the prompt was the only variable.

**What happened:** Every v1 exposure was removed. No apology, no concession, no
invented review process, no retention offer. The reply acknowledges the three
issues without accepting or disputing any of them, and states only that the owner
will respond within two business days.

Classification was correct as `mixed` — the message contains a cancellation, a
service complaint and a financial request, and treating it as a cancellation alone
would have routed it wrongly. `financial_request: true` and `escalate: owner` route
it to the person with authority rather than the coach who would otherwise have seen
it first.

`owner_note` gave the owner a decision summary rather than the self-report P8
produced, so the looser field definition worked here where it did not there.

One gap: the two-business-day timeframe was invented. Rule 4 permits committing to
"someone will respond and by when" without specifying what "when" is, so the model
supplied one. It happens to be reasonable, but it is a service commitment made by
the model rather than the business. The fix is to state the response window in the
context block rather than leaving it to be filled.

**On the pattern:** 76.25 → 90.25 is the largest gain in the library, and v1 was its
lowest score. The correlation is not incidental — the prompts that scored worst
unconstrained are the ones where an unconstrained model has the most it can commit
the business to. P9 had the widest scope for damage and the widest gain from
closing it.
