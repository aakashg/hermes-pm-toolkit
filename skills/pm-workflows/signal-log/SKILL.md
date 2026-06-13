---
name: signal-log
description: Capture customer signals from interviews, sales emails, support threads, and any raw customer feedback. Searches prior signals before logging. Detects patterns across persona, plan tier, and frequency. Trigger on "/signal-log", "log this interview", "what have we heard about X", "pull signal before my PRD".
version: 1.0.0
license: MIT
metadata.hermes.tags: []
---

# Customer Signal Logger

## When to Use

Trigger on:
- `/signal-log` followed by any pasted content
- "log this interview"
- "add this to signals"
- "log this customer email"
- "log this support thread"
- "what have we heard about [topic]"
- "pull signal before my PRD"
- "how many times has [pain] come up"
- "which personas mentioned [feature]"
- "is there a pattern around [topic]"

Two modes: **Logging mode** (user pastes raw input) and **Query mode** (user asks what has been heard).

---

## Logging Mode

Use when the user pastes interview notes, a sales email, a support thread, or any raw customer feedback.

### Step 1: Parse the input

Extract from whatever the user pastes:

```
Persona: [role, seniority, company size, industry]
Plan tier: [free / SMB / mid-market / enterprise / unknown]
Source type: [interview / sales email / support thread / churn survey / NPS / other]
Conversation date: [when it happened — not today's date]
Pain points: [specific friction, one per line]
Verbatim quotes: [exact words, in quotation marks]
Feature signals:
  - Explicit: [directly asked for by name]
  - Implied: [not requested but would solve the pain]
Sentiment: [FRUSTRATED / NEUTRAL / POSITIVE / CHURNING]
```

If any field is missing from the input, make a reasonable inference and mark it `[inferred]`. Do not leave fields blank.

### Step 2: Search prior sessions before saving

Before writing the log entry, search session memory for every pain point and feature signal extracted in Step 1. Use the exact canonical terms from SOUL.md's terminology section — not synonyms.

```
session_search: "[pain point — canonical term]"
session_search: "[feature signal]"
session_search: "[persona type] [pain]"
```

If prior matches exist, surface them immediately before logging the new entry:

```
⚡ Pattern match: "[pain]" — [N] prior occurrences
First flagged: [date] ([persona, source type])
Also flagged: [date], [date]
Personas: [list of roles]
Plan tiers: [list]
```

### Step 3: Apply pattern threshold rules

| Occurrences | Action |
|-------------|--------|
| 1st occurrence | Log, no flag |
| 2nd occurrence | Log + note: "Seen before ([date])" |
| 3rd occurrence | Log + flag PATTERN + write to USER.md |
| 4th+ occurrence | Log + flag PATTERN + write to MEMORY.md |

When the 3rd occurrence threshold is crossed, write to `~/.hermes/memories/USER.md` automatically:
```
[Pain] flagged [N] times across [persona types] ([plan tiers]) as of [date].
```

### Step 4: Append to log file

Append a new entry to `~/.hermes/signals/log.md` using this format:

```markdown
---
Date logged: [today]
Conversation date: [when it happened]
Source: [interview / email / support / churn / NPS]

Persona: [role] · [company size] · [industry] · [plan tier]
Contact: [first name only, or "anonymous"]

Pain points:
- [pain 1 — use canonical term from SOUL.md]
- [pain 2 — use canonical term from SOUL.md]

Verbatim quotes:
> "[exact quote]"
> "[exact quote]"

Feature signals:
- Explicit: [feature name]
- Implied: [feature name]

Sentiment: [FRUSTRATED / NEUTRAL / POSITIVE / CHURNING]
Session tags: [canonical pain terms used — for future search]

Pattern status: [Nth occurrence of "[pain]" / First occurrence — no pattern yet]
---
```

### Step 5: Confirm to user

Reply with a one-block summary:

```
Logged: [Persona], [company size], [plan tier]
Pain: [primary pain, one line]
Quote: "[best verbatim quote]"
Feature signal: [explicit] / [implied]
Sentiment: [FRUSTRATED / NEUTRAL / POSITIVE / CHURNING]

[If pattern detected:]
⚡ Pattern: "[pain]" — [N]th occurrence
First: [date] · Also: [dates] · Personas: [list] · Tiers: [list]
USER.md updated.

[If first occurrence:]
First occurrence of "[pain]". Pattern flag triggers at 3.
```

---

## Query Mode

Use when the user asks what has been heard about a topic — typically before writing a PRD or entering a planning session.

### Query triggers
- "what have we heard about [topic]"
- "pull signal before my PRD on [topic]"
- "how many times has [X] come up"
- "which customers mentioned [Y]"
- "is there evidence for [pain] across enterprise accounts"

### Query procedure

1. Search session memory using the canonical term and all listed synonyms from SOUL.md's terminology section
2. Scan `~/.hermes/signals/log.md` for matching entries
3. Return structured summary:

```
Signal summary: [topic]
Query ran: [timestamp]

Total occurrences: [N]
Date range: [first signal date] → [most recent]

By persona:
- [Role type]: [N] mentions
- [Role type]: [N] mentions

By plan tier:
- Enterprise (100+ seats): [N]
- Mid-market (20–99): [N]
- SMB (1–19 seats): [N]
- Free: [N]

Verbatim quotes (strongest 3):
1. "[quote]" — [Persona, date]
2. "[quote]" — [Persona, date]
3. "[quote]" — [Persona, date]

Pattern status: [PATTERN (3+ occurrences) / EMERGING (2 occurrences) / SINGLE INSTANCE]

Feature signals across all entries:
- Explicit requests: [list what was directly asked for]
- Implied by pain: [list what the pain implies]
```

---

## Terminology Is Everything

Session search is keyword-based, not semantic.

If you log "manual permissions" in October and query "access control setup" in January, you get zero matches — even though they mean the same thing.

Use the canonical terms defined in `SOUL.md` every time you log an entry. If you catch yourself using a synonym, add it to SOUL.md's terminology section immediately. This takes 10 seconds. Missing a match in a PRD query costs a research meeting.

---

## What the Log Looks Like After 3 Months

A single PRD query takes under 30 seconds and returns:
- Frequency count by persona type and plan tier
- Three best verbatim quotes ranked by recency
- Pattern flag with first-seen date and all occurrences
- Feature signals broken down by explicit vs. implied

The research meeting you would have scheduled doesn't happen.
