# Decision Log
# Auto-populated by /decision-log skill. Do not edit manually.
# Query with: /decision-log — have we looked at [feature] before
# Copy this file to: ~/.hermes/decisions/log.md

---
# EXAMPLE ENTRY — delete this before using, or leave it as a format reference

Feature: offline-mode
Date: 2025-11-14
Evaluation number: 1

Decision: DEPRIORITIZED

Reason:
Zero paying customer requests in 4 months of signal logging across 47 interviews.

What was rejected:
- Lightweight read-only offline mode — rejected because it solves 20% of the use case and creates a maintenance surface
- Partnering with a third-party sync provider — rejected because it introduces a dependency we cannot control

Signals that drove this call:
- 0 enterprise mentions in signal-log from Aug–Nov 2025
- 2 free-tier user requests (both single-person teams, low ARR)
- Engineering estimate: 5 sprints for full implementation

Confidence: HIGH
Confidence note: No paying customer has raised this unprompted in 4 months. Pattern is absence, not noise.

REVISIT IF: 3 or more enterprise accounts (100+ seats, paid tier) explicitly request offline mode in the same quarter.

Flip condition status: NOT MET
Last checked: 2025-11-14
---

---
# EXAMPLE ENTRY 2

Feature: offline-mode
Date: 2026-01-22
Evaluation number: 2

Decision: DEPRIORITIZED (reconfirmed)

Reason:
Engineering cost revised upward to 6 sprints after architecture review. Still zero paying customer requests.

What was rejected:
- Revisiting at Q2 planning — no new signal to justify reopening

Signals that drove this call:
- Still 0 enterprise mentions in signal-log through Jan 2026
- Feb 18: Meridian ($180K ARR) mentioned it as "nice to have" — not explicit request
- Engineering architecture review added 1 sprint to estimate

Confidence: HIGH

REVISIT IF: 3 or more enterprise accounts (100+ seats, paid tier) explicitly request offline mode in the same quarter.

Flip condition status: PARTIAL — 1 of 3 required (Meridian, Feb 18)
Last checked: 2026-01-22
---
