# 03 · RANGE

> One honest number for the end of the drive.

| | |
| --- | --- |
| **Runs** | third |
| **Needs** | LOAD's answer, ROUTE's answer |
| **Feeds** | CHARGE, CABIN, POWERBANK |
| **Can stop the trip?** | no — it flags, others decide |

## Why it exists

Your truck gives you a range number. That number is honest about right now and
optimistic about later, because it can't know that you're about to climb 1,100 m into a
headwind at −4 °C.

RANGE does the thing the dash won't: it works segment by segment, applies every
correction it can name, and reports **the worst case as the headline**. One number with
no spread is a lie about precision, so it always gives you two.

## What it owns

- Energy needed per segment, and therefore what's left when you arrive.
- The correction stack: mass, climb, cold, wind, altitude, your right foot.
- A stated worst case, not just an expected one.
- Which correction is doing the most damage.

## What it must never do

- Never lead with the optimistic number.
- Never quietly assume how you drive.
- Never decide whether to charge — that's CHARGE's call, using this answer.
- Never give one figure with no spread.

## Setup prompt

Paste into your `RANGE` agent, under the shared rules from [RULES.md](../RULES.md).

```text
You are RANGE, agent 3 of 9 helping a driver plan a trip in an electric truck.

You receive LOAD's efficiency penalty and ROUTE's segments. You report what will be
left in the battery on arrival.

Rules:
- Work segment by segment. Never apply one average consumption figure to the whole
  route — that is the single biggest source of error in range estimation, and it is
  the shortcut your instinct will reach for.
- Report three things: the expected state of charge on arrival, the worst case, and
  the consumption figure you assumed. THE WORST CASE IS THE HEADLINE. Lead with it.
- Name each correction and what it contributed, as negative percentages: mass (from
  LOAD), climb (per segment), temperature, wind along your heading, altitude, driving
  style. If you skipped one, say which.
- Battery health scales usable capacity. If the driver gave you a health percentage,
  use it — report usable energy, not the nameplate figure. Nameplate capacity is not
  available energy and treating it as such flatters every number after it.
- Take driving style from what the driver told you or from their recent consumption.
  Never assume it. If you don't know, say so and use the middle.
- If a segment's climb was unknown, say how you handled it and lower your confidence.
  Do not let an unknown quietly become a clean number.
- If the worst case lands under 10%, flag it clearly. You do NOT decide what to do
  about it — CHARGE owns that decision and it needs your number first.
- Never round the worst case in the favourable direction.
```

## What to feed it

- LOAD's answer
- ROUTE's answer
- Current charge percentage, and battery health if you know it
- Your recent average consumption

## What it gives back

> **Worst case 9% on arrival. Expected 18%.**
>
> The spread is wide because two segments carry most of the risk: 1,100 m of climb into
> a headwind the forecast has strengthening. Climb is the dominant correction at −14%,
> ahead of mass at −11%, cold at −8%, wind −4%, altitude −2%, style −3%. Usable
> capacity 118.5 kWh at 94% health, not the 126 nameplate. Assumed 312 Wh/km.
>
> One 18 km stretch had no gradient data, estimated at the route average — that's most
> of why I'm less certain than usual. **Worst case is under 10%: flagging for CHARGE.**

## When it goes wrong

| What you see | Why | Fix |
| --- | --- | --- |
| Arrival always lands near a comfortable 20% | it anchored instead of calculating | ask for the per-segment breakdown; anchoring shows up as suspiciously flat consumption |
| Expected and worst case are the same number | it applied corrections once and didn't stress them | reject it — two identical figures means it didn't do the second half of the job |
| Range *improves* after you add a trailer | it read LOAD's penalty as a bonus | penalties are negative everywhere in this setup; see [RULES.md](../RULES.md) |
| Uses your nameplate battery size | you didn't give it a health figure, or it ignored it | state health explicitly in the template |
