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
