# Hermes PM Toolkit

Self-writing skills for product managers running on the Hermes agent runtime. Built to accompany [the post on the Monday Gap](#) — drop this folder into `~/.hermes/` and the three workflows load automatically.

## What's in here

```
.
├── SOUL.md                                  # Your PM context — fill in [brackets] before first session
├── memories/
│   └── USER.md                              # User profile + auto-updated decision patterns
├── signals/
│   └── log.md                               # Customer signal log (auto-populated)
├── decisions/
│   └── log.md                               # Decision log with REVISIT IF flip conditions
├── skills/pm-workflows/
│   ├── competitive-intel/SKILL.md           # Use Case 1 — Monday briefing with pattern detection
│   ├── signal-log/SKILL.md                  # Use Case 2 — queryable customer signal
│   └── decision-log/SKILL.md                # Use Case 3 — decision history with flip triggers
└── 30-day-rollout.md                        # The order I ran the use cases in
```

## Install

```bash
git clone https://github.com/<you>/hermes-pm-toolkit.git
cd hermes-pm-toolkit

cp SOUL.md ~/.hermes/SOUL.md
cp memories/USER.md ~/.hermes/memories/USER.md
mkdir -p ~/.hermes/signals ~/.hermes/decisions
cp signals/log.md ~/.hermes/signals/log.md
cp decisions/log.md ~/.hermes/decisions/log.md
cp -r skills/pm-workflows ~/.hermes/skills/
```

Then open `~/.hermes/SOUL.md` and fill in every `[bracket]`. This is the 10 minutes that determines whether the next three weeks produce generic output or output calibrated to you.

## Use

- `/competitive-intel` — run a competitive briefing now, or schedule the Monday cron job (see the skill file)
- `/signal-log` — log a customer interaction, or query "what have we heard about [topic]"
- `/decision-log` — log a decision with a REVISIT IF flip condition, or check whether something has come up before

## The 30-day arc

See [30-day-rollout.md](30-day-rollout.md). Short version: install Use Case 1 in week 1, add Use Case 2 in week 2, add Use Case 3 in week 3. Week 4 is when the compounding shows up. Most people quit before week 4.

## Contributing

PRs welcome — especially new PM skills that follow the same pattern (clear `When to Use`, an explicit procedure, an auto-update step that writes to `MEMORY.md` or `USER.md`).

## License

MIT — see [LICENSE](LICENSE).
