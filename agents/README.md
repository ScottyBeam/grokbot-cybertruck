# The nine agents

Nine narrow agents in a fixed order, and one arbiter on top. Each file has the agent's
job, its full setup prompt, what to feed it, what it gives back, and what it looks like
when it goes wrong.

```
LOAD → ROUTE → RANGE → CHARGE → CABIN
 01     02      03      04       05
                                  │
SERVICE  (can veto everything) ───┤
 06                               │
GUARD ────────────────────────────┤
 07                               │
POWERBANK ────────────────────────┤
 08                               │
                                  ▼
                    CHIEF OF STAFF · 09
                                  ▼
                            one sentence
```

| # | Agent | Job | Needs |
| - | ----- | --- | ----- |
| 01 | [LOAD](01-load.md) | weighs what you're carrying and what it costs | your numbers |
| 02 | [ROUTE](02-route.md) | draws a road, not a line | 01 |
| 03 | [RANGE](03-range.md) | one honest number for the end of the drive | 01, 02 |
| 04 | [CHARGE](04-charge.md) | asks whether you need a stop at all | 02, 03 |
| 05 | [CABIN](05-cabin.md) | prices comfort in kilometres | 03, 04 |
| 06 | [SERVICE](06-service.md) | holds a veto over all of it | 01, 02, 04 |
| 07 | [GUARD](07-guard.md) | watches while you're away, says almost nothing | 03 |
| 08 | [POWERBANK](08-powerbank.md) | never borrows from tomorrow's trip | 03, 04, 07 |
| 09 | [CHIEF OF STAFF](09-chief-of-staff.md) | settles the arguments, writes one sentence | all eight |

## Three things worth understanding before you set them up

**Each agent is narrow enough to be wrong out loud.** LOAD can't hide a bad weight
estimate behind a range figure, because it never produces one. When something is wrong,
you can see which agent got it wrong — and a mistake you can point at is a mistake you
can fix.

**The order is a dependency, not a preference.** RANGE without LOAD's number is a
guess. CHARGE without RANGE's number is a guess with a receipt attached. Running them
out of order, or in parallel to save time, quietly removes everything that makes this
better than one long prompt.

**One agent holds the veto and it isn't the one in charge.** SERVICE can stop the trip;
CHIEF OF STAFF can't overrule it. Keep authority and arbitration in different places
and the setup won't talk itself into a bad drive.

## Where to start reading

1. [CHIEF OF STAFF](09-chief-of-staff.md) — start at the output and work backwards.
   It's the agent that makes the other eight worth running.
2. [LOAD](01-load.md) — the shape every other agent file follows.
3. [SERVICE](06-service.md) — why the veto is separate, which is the least obvious
   decision in the whole design.
4. [../RULES.md](../RULES.md) — the rulebook all nine share.
