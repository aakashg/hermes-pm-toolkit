---
name: competitive-intel
description: Run competitive monitoring and generate a structured briefing with pattern detection across accumulated runs. Fires automatically on the Monday 8 AM cron schedule if configured. Trigger on "run competitive briefing", "what did competitors ship", "competitive monitoring", "check [competitor] changelog".
version: 1.0.0
license: MIT
metadata.hermes.tags: []
---

# Competitive Intelligence Skill

## When to Use

Trigger on:
- "run competitive briefing"
- "what did competitors ship this week"
- "competitive monitoring"
- "check [competitor] changelog"
- "what has [competitor] been doing"
- `/competitive-intel` slash command
- Automatic: Monday 8 AM cron job (once configured below)

Do NOT trigger on:
- General strategy questions that don't require a live fetch
- Questions already answered by a briefing from the same week

---

## Pre-Run: Load Session History

Before fetching anything, search session memory for each competitor. This is what separates week 6 from week 1 — without this step, you are running a snapshot, not building a picture.

```
session_search: "[Competitor A] changelog"
session_search: "[Competitor B] changelog"
session_search: "[Competitor C] changelog"
```

Load all prior results. You will compare against them in Step 3.

---

## Procedure

### Step 1: Fetch competitor pages

For each URL listed in SOUL.md under "My Competitors":
1. Fetch the changelog, blog, or releases page
2. Extract all items published in the last 7 days
3. Check publish date metadata if not visible inline
4. Items with no visible date: log as `DATE UNKNOWN`, include with a note — do not skip

### Step 2: Structure raw items

For each item extracted, record:

```
Competitor: [name]
Item: [one-line description of what shipped or was published]
Date: [publish date or DATE UNKNOWN]
Category: [feature / pricing / partnership / hiring / content / regulatory]
Source URL: [direct link]
```

### Step 3: Compare against session history

For each item, check it against the prior session search results loaded in the Pre-Run step.

| Condition | Tag |
|-----------|-----|
| Not seen in any prior run | `NEW` |
| Seen in exactly 1 prior run | `ONGOING (seen once, [date])` |
| Seen in 2 consecutive runs | `ONGOING (seen twice, first: [date])` |
| Seen in 3+ consecutive runs | `PATTERN — [your interpretation]` |
| Zero updates this week, had updates last week | `WATCH — silence after activity` |
| Silent for 3+ consecutive weeks | `WATCH — extended silence since [last update date]` |

**PATTERN interpretation guide:**
- 3+ consecutive pricing-adjacent updates → repositioning or monetization push
- 3+ consecutive enterprise/compliance content → moving upmarket deliberately
- 3+ consecutive API or developer-facing updates → platform play underway
- 3+ consecutive updates to one feature area → accelerating a specific bet
- Sustained activity followed by sudden silence → possible launch prep or internal shift

### Step 4: Write the briefing

Output in this format:

```
Competitive Briefing: [date range]
Generated: [timestamp]

---

[COMPETITOR A]
• [Item] — NEW
• [Item] — PATTERN: Accelerating data features. Started [date].
• [Item] — ONGOING (3rd week)

[COMPETITOR B]
• [Item] — NEW
• [Item] — ONGOING (seen once, [date])

[COMPETITOR C]
• WATCH: Silent 5 weeks. Last update [date] (pricing page).

---
SUMMARY

Patterns this week:
[List every PATTERN item with a one-line interpretation]

Items to watch:
[List every WATCH item]

Notable new items:
[Top 2–3 NEW items worth flagging to the team]

No changes this week: [Competitors with zero updates]
```

### Step 5: Deliver and save

1. Post to Slack home channel configured in `~/.hermes/.env` as `SLACK_HOME_CHANNEL`
2. Save full briefing to session memory tagged `competitive-briefing-[date]`
3. This saved entry is what future runs search against — do not skip this step

---

## Slack Formatting Block

When posting to Slack, use this structure for clean readability:

```
📊 *Competitive Briefing: [date range]*

*[COMPETITOR A]*
• [Item] — `NEW`
• [Item] — `PATTERN: Accelerating data features. Started [date].`
• [Item] — `ONGOING (2nd week)`

*[COMPETITOR B]*
• [Item] — `NEW`

*[COMPETITOR C]*
• `WATCH: Silent 5 weeks (last update: [date], pricing page)`

---
*Patterns this week:* [One sentence]
*Watch list:* [One sentence]
*Full briefing saved to session memory.*
```

---

## One-Time Setup: Monday Cron Job

Type this into Hermes once. You will not touch it again.

```
Every Monday at 8 AM, run the competitive-intel skill against:
- [competitor1.com/changelog]
- [competitor2.com/blog]
- [competitor3.com/releases]

Compare with everything flagged in prior scans.
Mark items NEW or ONGOING. Surface any 3-week patterns.
Post to my Slack DM. Save as a scheduled job.
```

Confirm the job was created with `hermes cron list`.

---

## One-Time Setup: Webhook Subscriptions

After the first manual run, set up event-driven alerts so you stop polling. Type this into Hermes once:

```
Subscribe to RSS feeds for:
- [competitor1.com/blog/feed]
- [competitor2.com/changelog/feed]
- [competitor3.com/releases/feed]

When a new item arrives, post a one-line alert to my Slack DM immediately.
Format: "🔔 [Competitor]: [title] — [link]"
Save as a webhook subscription.
```

Confirm with `hermes webhook subscribe`. Zero tokens. Zero latency. This replaces Zapier for competitor monitoring.

---

## Monthly Optimization Pass

Run `/insights` at the end of each month. Check which competitor pages consumed the most tokens and returned the least useful signal. Remove them. The briefing gets cheaper and sharper at the same time.

---

## What Week 1 vs Week 6 Looks Like

**Week 1 output:**
```
Competitive Briefing: Apr 28 – May 5
Competitor A: Shipped analytics dashboard. No pricing change.
Competitor B: Blog post on enterprise security.
Competitor C: No updates this week.
Summary: First scan. No patterns yet.
```

**Week 6 output:**
```
Competitive Briefing: Jun 2 – Jun 9
Competitor A: Third analytics update in 6 weeks.
  PATTERN: Accelerating data features. Started Apr 28.
Competitor B: Compliance positioning in 4 consecutive posts.
  ONGOING: Followed SOC2 announcement. Looks deliberate.
Competitor C: Silent 5 weeks. Last update Apr 21 (pricing page).
  WATCH: Unusual silence. Possible internal shift.
Summary: Competitor A is accelerating. Competitor B is repositioning for enterprise. Competitor C has gone quiet.
```

Same prompt. The agent learned what mattered from accumulating runs.
