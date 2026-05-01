# 30-Day Rollout Plan

The order I ran the three use cases in. You can do them in parallel, but if you do them serially in this order, the compounding shows up earlier and you don't burn out on setup.

The principle: install one workflow, get it loading on its own, *then* add the next. Adding all three on day one means you debug three skills at once and abandon all three by week two.

---

## Before Day 1 (20 minutes, one-time)

Do these once. Do not skip. Every shortcut here costs you a week of generic output later.

1. Install Hermes and run `hermes setup`.
2. Pick a model (NVIDIA NIM is the fastest on-ramp; Ollama if you want fully local; your own Anthropic / OpenAI / Google key if you already pay for one).
3. Connect *one* messaging platform. Don't try to wire up all of them on day one.
4. Run `hermes backup` so you have a snapshot to restore from if you break something.
5. Copy the toolkit:
   ```bash
   cp SOUL.md ~/.hermes/SOUL.md
   cp memories/USER.md ~/.hermes/memories/USER.md
   cp -r skills/pm-workflows ~/.hermes/skills/
   mkdir -p ~/.hermes/signals ~/.hermes/decisions
   cp signals/log.md ~/.hermes/signals/log.md
   cp decisions/log.md ~/.hermes/decisions/log.md
   ```
6. **Open SOUL.md and fill in every `[bracket]`.** This is the most important 10 minutes of the entire setup. Skip it and every session for the next three weeks will produce generic output. I did. Don't.

---

## Week 1 — Install Use Case 1 only

**Goal:** prove the loop runs without you. Do not add Use Case 2 or 3 yet.

- Day 1: Run `/competitive-intel` manually with 3 competitor URLs from SOUL.md. Read the output. It will be a list, not a briefing. That's expected.
- Day 1: Schedule the Monday 8 AM cron job using the prompt in `competitive-intel/SKILL.md`.
- Day 2: Confirm with `hermes cron list`. Close your laptop normally Sunday night.
- Day 3 (Monday): Read the auto-generated briefing in Slack. It will look like Week 1 in the skill file.
- Day 4–7: Don't touch it. Do not "improve the prompt." Don't add competitors yet.

**What to expect:** the briefing reads like a list. There are no patterns yet. You will be tempted to declare it underwhelming. Resist.

---

## Week 2 — Add Use Case 2 (signal log)

**Goal:** start accumulating customer signal so the agent has something to pattern-match against later.

- Log every customer interaction into `/signal-log` for the full week. Interviews, sales forwards, support flags, CS notes. Past notes count — paste in the last 4–6 weeks of interview notes if you have them sitting in a doc.
- Don't query yet. Just log. The skill needs ~10–15 entries before search returns anything useful.
- Continue letting Use Case 1 run on its Monday cron. Do not modify it.

**What to expect:** logging feels like overhead with no return. The return arrives in Week 4.

---

## Week 3 — Add Use Case 3 (decision log)

**Goal:** start the decision history that will pay off in Q3 planning.

- Every meeting where you decide to build, defer, or kill something, run `/decision-log` afterward. Capture the decision, the reasoning, the signals that drove it, and *always* the REVISIT IF condition.
- The REVISIT IF field is non-negotiable. A decision log without flip conditions is a record, not a system. I ran it without flip conditions for three weeks and the log was useless.
- Backfill: log the last 3–5 decisions you remember making in the past quarter. They feed the pattern detection.

**What to expect:** week 3 still feels like overhead. Trust the rollout.

---

## Week 4 — The step change

This is when most people quit. Don't.

- Run `/insights 30` for the first time. You will see token usage by skill, which sessions ran, which decisions got revisited.
- Read your competitive briefing this Monday. Compare it to the one from Week 1. The agent has stopped re-explaining what each competitor is. It's surfacing patterns.
- Query the signal log: "What have we heard about [a topic you're about to write a PRD on]?" The answer will surprise you.
- Open USER.md. Hermes has started writing entries in the **Decision Patterns** section. Read them. Some will feel uncomfortably accurate.

**What to expect:** the system starts feeling like it's working *with* you instead of being prompted by you. This is the compounding the post describes.

---

## Week 5–8 — Let it accrue

Do not add more skills. Do not "optimize" the existing ones. The compounding only works if the procedure is stable.

- Week 5: Add `/btw` and `/branch` to your habits. They preserve context mid-session instead of restarting.
- Week 6: Run a `/insights 30` pass. Cut competitor URLs that consume tokens and surface nothing. Cut signal sources that never produce patterns.
- Week 7: Use the decision log *before* your next planning meeting. Run the query in Use Case 3. The meeting that would have run 45 minutes will run 6.
- Week 8: Open `~/.hermes/skills/pm-workflows/competitive-intel/SKILL.md`. Compare it to the version in this repo. The agent will have rewritten parts of it. Read what changed and why.

---

## After Day 30

The system is now load-bearing. A few habits to keep:

- Run `hermes backup` weekly. It's free.
- Quarterly: prune `SOUL.md`. Anything you wrote that you stopped relying on, delete. Bloat causes the model to ignore real instructions.
- Every quarter: run `/insights 90`. Decide which skills earned their keep and which didn't.
- When a skill rewrite goes wrong, `/snapshot restore <id>` rolls it back. Don't be afraid to let the agent rewrite skills — you can always revert.

The bet is not that any single output is dramatically better than what you'd produce yourself. The bet is that the same output, every Monday, with no incremental work, compounds into something a static toolchain cannot match.

That only shows up if you let the rollout run.
