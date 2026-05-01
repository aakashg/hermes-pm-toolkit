---
name: decision-log
description: Log and retrieve planning decisions with full reasoning, rejected alternatives, and flip conditions. Automatically searches prior decision history before any planning interaction. Stops the same debate from running twice. Trigger on "/decision-log", "should we build X", "deprioritize", "why did we decide", "have we looked at this before".
---

# Decision Logger

## When to Use

Trigger on:
- `/decision-log` followed by any content
- "should we build [feature]"
- "let's deprioritize [feature]"
- "push [feature] to next quarter"
- "kill [feature]"
- "why did we decide [X]"
- "we keep debating [feature]"
- "have we looked at [feature] before"
- "what is the current status of [feature]"
- Any planning session where a build / kill / defer decision is being made or questioned

---

## Pre-Session Check — Always Run First

Before any planning interaction, automatically run:

```
session_search: "[feature or topic being discussed]"
```

If a prior decision exists, surface it before anyone starts talking:

```
📋 Prior decision found: [feature]

Evaluation [N], [date]:
Decision: [DEPRIORITIZED / APPROVED / DEFERRED / KILLED / INVESTIGATE]
Reason: [one sentence — exact reason from the log]
Signals that drove it: [list]
Rejected alternatives: [what else was considered and why it was rejected]
Confidence: [HIGH / MEDIUM / LOW]

REVISIT IF: [the specific condition from the original log entry]
Condition status: [MET / NOT MET / PARTIAL — with evidence and source]

Recommendation: [Hold — condition not met / Escalate — condition met / Partial — bring to team]
```

If this surfaces before anyone starts talking, the planning meeting takes 6 minutes instead of 45.

---

## Logging a New Decision

### Step 1: Extract from conversation

Listen for and capture:
- The actual call: build / defer / kill / investigate further
- What was explicitly considered and rejected, and the specific reason
- The signals that drove the decision (source, date, plan tier, ARR if known)
- The flip condition — what specific, measurable thing would change this decision
- Confidence level in the call

**If the flip condition is not stated, ask:**
*"What specific, measurable condition would make you revisit this?"*

Do not log a decision without a REVISIT IF field.

A decision without a flip condition is a diary entry. A decision with a flip condition is institutional memory.

**Good flip conditions — specific and measurable:**
```
3+ enterprise accounts (100+ seats, paid) explicitly request it in the same quarter.
Churn survey surfaces this as top-3 reason in two consecutive quarterly surveys.
Competitor ships this and our NPS drops more than 5 points within 60 days.
Engineering cost estimate drops below 2 sprints after architecture review.
```

**Bad flip conditions — vague and unmeasurable:**
```
"If things change."          ← not a condition
"If we get more signal."     ← not specific
"If leadership asks."        ← not an outcome
"When the time is right."    ← meaningless
```

### Step 2: Append to log file

Append a new entry to `~/.hermes/decisions/log.md`:

```markdown
---
Feature: [name — use exact canonical term from SOUL.md]
Date: [today]
Evaluation number: [1 if first time / 2 / 3 — increment if prior evals exist for this feature]

Decision: [DEPRIORITIZED / APPROVED / DEFERRED / KILLED / INVESTIGATE]

Reason (one sentence, specific — not generic):
[Not "not enough signal." Be exact: "Zero paying customer requests across 47 logged interviews over 4 months."]

What was considered and rejected:
- [Alternative A] — rejected because [specific reason]
- [Alternative B] — rejected because [specific reason]

Signals that drove this call:
- [Signal 1: source, date, plan tier, ARR if known]
- [Signal 2: source, date, plan tier]
- [Absence of signal: e.g., "zero enterprise mentions in signal-log Aug–Nov 2025"]

Confidence: [HIGH / MEDIUM / LOW]
Confidence note: [Why this level — e.g., "HIGH: No paying customer has raised this in 4 months unprompted."]

REVISIT IF: [Specific, measurable condition — not vague]

Flip condition status: NOT MET
Last checked: [today]
---
```

### Step 3: Write one-liner to USER.md

After logging, append to `~/.hermes/memories/USER.md` under "Open Decisions":

```
[Feature] — [DECISION] [date] — [reason in 8 words]. Flip: [condition in 10 words].
```

Examples:
```
Offline mode — DEPRIORITIZED Nov 14 — zero paying customer requests. Flip: 3+ enterprise explicit requests.
SSO — DEFERRED Jan 22 — eng cost 6 sprints. Flip: cost drops below 2 sprints.
Mobile app — KILLED Mar 3 — no enterprise use case found. Flip: 5+ enterprise accounts request native app.
```

### Step 4: Confirm to user

```
Logged: [Feature] — [DECISION]
Reason: [one sentence]
REVISIT IF: [condition]
Evaluation [N] of [N total logged for this feature].

[If prior evaluations exist:]
Prior history: Eval 1, [date] — [decision then and reason]

[If flip condition has partial progress:]
Condition progress: [N of X required]
Evidence so far: [source, date, what was said]
```

---

## Querying a Prior Decision

Use when the user asks whether something was decided before.

**Query triggers:**
- "have we looked at [feature] before"
- "why did we deprioritize [X]"
- "what is the status of [feature]"
- "has the condition been met for [feature]"

**Query procedure:**

1. Run `session_search: "[feature name]"` and scan `~/.hermes/decisions/log.md`
2. If found, return full history with all evaluations and flip condition status:

```
Decision history: [feature]

Evaluation 1, [date]:
[DECISION]. [Reason — exact from log].
Revisit if: [condition].

Evaluation 2, [date]:
[DECISION]. [Reason — exact from log].
Same flip condition.

Flip condition status:
[date]: [Evidence — who said it, what was said, ARR if known]
[date]: [Evidence]
Current count: [N of X required].

Recommendation: [Hold — condition not yet met / Escalate — condition met]
```

---

## Flip Condition Monitoring

During every decision-log session, automatically check whether any open decisions have had their flip condition partially or fully met.

1. Pull all DEPRIORITIZED / DEFERRED entries from `~/.hermes/decisions/log.md`
2. For each entry, search session memory and signal-log for evidence of the flip condition using canonical terms
3. If new evidence exists:

```
⚠️ Flip condition update: [Feature]
Condition: [the original condition]
New evidence: [source, date, what was said, ARR if relevant]
Current count: [N of X required]
Recommendation: [Hold — not yet met / Escalate to team — condition met]
```

---

## What the Log Delivers at 10 Weeks

Any feature raised in planning returns:
- Every prior evaluation with exact dates and reasoning
- What alternatives were rejected and why
- The signals that drove each call — cross-referenced with signal-log
- Whether the flip condition has been partially or fully met, with evidence
- A recommendation: hold or escalate

The meeting that would have run 45 minutes takes 6.

By month three, Hermes will have written entries to USER.md from observing your decision pattern across sessions — things like which signals you consistently act on, which plan tiers you weight, which conditions you set and whether you tend to hold them. These entries are worth reading. They show how you actually decide, not how you intend to.
