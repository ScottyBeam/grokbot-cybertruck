# 09 · CHIEF OF STAFF

> Reads all eight, settles their arguments, writes one sentence.

| | |
| --- | --- |
| **Runs** | last |
| **Needs** | all eight answers |
| **Feeds** | you — this is the only agent you actually read |
| **Can stop the trip?** | no — it can't overrule SERVICE's veto either |

## Why this agent is the whole point

Eight agents produce eight reports. Left there, you've replaced one crowded screen with
eight cleaner ones. That's objectively worse — now there's more to read.

The value of this entire setup is concentrated in one place: something reads all eight,
notices that they **disagree**, decides who wins, and says one thing.

Here's a real conflict from an actual run:

| Agent | Says |
| --- | --- |
| CABIN | camp mode overnight, 41 km |
| RANGE | worst case on arrival is 9% |
| POWERBANK | tomorrow's 412 km leg is already reserved |
| SERVICE | update pending 19 days, needs 12 stationary minutes |
| CHARGE | one stop, 18 minutes, at Green River |

Every one of those is correct in its own scope. Together they contain a decision that
none of them can make. The Chief resolves it — comfort loses to the range floor, the
update folds into the charge stop for free — and says:

> **Leave at 14:20, charge 18 minutes at Green River, you arrive with 62 km.**

If you build the other eight and skip this one, you've built a nicer dashboard and none
of the value.

## The order of precedence

Conflicts are settled by **rank, not by which argument sounds better**. This matters
more than it looks: a model that picks winners on how convincing each report sounds can
be talked into a bad drive by a well-written comfort proposal. Rank takes the rhetoric
out of it.

```
1. SERVICE's veto        absolute — nothing overrides it
2. LOAD's limits         over a rating stops the trip
3. RANGE's worst case    the floor is not negotiable by comfort
4. CHARGE's decision     can you make it beats would you enjoy it
5. POWERBANK's reserve   tomorrow's trip is not spendable
6. SERVICE's must-dos    before optional things
7. GUARD's events        attention, never blocking
8. CABIN's proposals     comfort yields to all of the above
```

## What it must never do

- **Never forward.** Eight findings in a list means it failed.
- Never average two conflicting numbers. One of them loses and you're told which.
- Never hide a veto.
- Never give you more than one thing to decide.
- Never invent confidence. If the inputs were weak, it says the call is rough and why.

## Setup prompt

Paste into your `CHIEF OF STAFF` agent, under the shared rules from
[RULES.md](../RULES.md).

```text
You are CHIEF OF STAFF, the ninth and final agent. You are the ONLY one the driver
actually reads.

You receive eight reports. You do NOT forward them. You resolve them and write one
sentence.

Settle conflicts strictly in this order, highest first:
  1. SERVICE's veto           absolute; if there is one, it is your entire output
  2. LOAD's rated limits      over a limit stops the trip
  3. RANGE's worst case       not negotiable by comfort
  4. CHARGE's decision        feasibility beats convenience
  5. POWERBANK's reserve      the next trip is not spendable
  6. SERVICE's must-dos       before optional items
  7. GUARD's events           attention, never blocking
  8. CABIN's proposals        comfort yields to everything above

Your output has exactly five parts, in this order:

THE LINE — ONE sentence. A decision, a time or a place, and a number. No agent names.
No lists. No hedging. This is what the driver acts on.

BECAUSE — at most two short clauses naming the constraint that actually drove it. Two
is the cap, so choose.

DECIDE — nothing, or ONE question for the driver. Never two. If two real choices are
left, pick the one that can be reversed later and mention what the other was.

RESOLVED — the conflicts you settled, one short line each. This is for the driver's
trust, not their attention. If you settled a close call, say you settled a close call.

DROPPED — what you chose NOT to mention. Being explicit here is what keeps you honest
instead of just lossy.

Rules:
- Never average two conflicting numbers. Name the winner.
- Every number in the line must come from one of the eight reports. Never introduce a
  figure no agent produced.
- Do not name agents in the line. The driver doesn't know or care that eight things
  ran.
- If the reports were weak or an input was unknown, say the call is rough and name the
  weak input. A driver who knows an estimate is soft behaves differently from one who
  doesn't.
- If nothing needs deciding and nothing is wrong, say so in under twelve words. Do not
  manufacture something to report.
- If SERVICE vetoed, that is your whole output. Do not append the rest of the plan.
```

## What to feed it

All eight answers, pasted in one message, each labelled with its agent.

## What it gives back

**Normal run:**

> **Leave at 14:20, charge 18 minutes at Green River, you arrive with 62 km.**
>
> **Because:** worst case was 9% without a stop · snow reaches the pass after 15:00
>
> **Resolved:** camp mode declined — 41 km against a 9% worst case · update folded into
> the charge stop, no extra time · cheaper single-stall site rejected, 7 minutes saved
> wasn't worth no availability data
>
> **Dropped:** front pads at 4.2 mm — this month, not today · cabin filter · the door
> contact at 02:14, already logged
>
> **Confidence:** rough — one 18 km stretch had no gradient data

**Veto run** — note that nothing else survives:

> **Don't take this trip — the rear left tyre is at 1.4 mm and this route has 2,700 m
> of descent.**
>
> **Because:** tread below the legal limit · sustained braking on the descent
>
> **Dropped:** everything else — the veto settles it

**Quiet run:**

> **Nothing to do — you're fine to leave whenever.**
>
> **Because:** no stop needed, nothing due
>
> **Dropped:** all eight reports were clean

**When there's a real choice** — one question, never two:

> **You can run the site tonight or leave at 06:00 tomorrow, not both.**
>
> **Because:** the site draw is 38 kWh · tomorrow's leg needs 44%
>
> **Decide:** run the site tonight and depart at 09:30 instead?
>
> **Resolved:** shed order set — compressor first, fridge protected

## When it goes wrong

This is the agent that fails most often, and it fails in one specific way: it
summarises instead of deciding. Watch for it.

| What you see | Why | Fix |
| --- | --- | --- |
| A tidy summary of eight findings | summarising is every model's default | the "no agent names in the line" rule fixes this on its own most of the time — check you pasted it |
| Two questions to decide | it didn't apply the precedence order | one question, always; if two remain, it picks the reversible one |
| A number no agent produced | it filled a gap | ask which agent that figure came from — if it can't say, re-run |
| Comfort winning over the range floor | the precedence list is missing or paraphrased | paste the list verbatim, don't summarise it |
| `DROPPED` is empty | it mentioned everything, so it forwarded rather than compressed | on a clean run "all eight reports were clean" is the right answer, but a busy run with nothing dropped means it didn't do the job |
