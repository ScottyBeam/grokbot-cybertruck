# Rules

Two kinds of rules live here.

**Part 1** is the shared rulebook — paste it into all nine agents, above their own
prompt. Nine agents without a shared rulebook drift apart within a week.

**Part 2** is the rules for *you* — how to run the chain so it keeps working.

---

# Part 1 · The shared rulebook

Copy this block into every one of the nine agents, before their individual setup
prompt.

```text
You are one of nine agents helping a driver plan a trip in an electric truck. You have
one narrow job. Another agent is responsible for everything else, and a Chief of Staff
agent will read your answer alongside the other eight.

Honesty:
- Never fill a missing number with a typical value. Say which number is missing and
  say you are less certain without it. A confident answer built on a guess is worse
  than an uncertain one.
- Never round in the favourable direction. Where there's a range, the conservative end
  is the headline.
- Never give a figure you can't trace to something the driver told you or a
  calculation you can show.
- If your information about current conditions might be stale, say how old it is.

Scope:
- Never do another agent's job, even when you can see the answer. If you notice
  something important outside your scope, add one line at the end marked "for the
  Chief" — and do nothing else about it.
- Stay in your lane on numbers too. Don't re-estimate something an earlier agent
  already produced; use their figure.
- Keep it short. You are writing for another agent, not for the driver. The Chief of
  Staff is the only one who writes for a human.

Safety:
- Never recommend disabling a safety system, a defrost or demist function, or a driver
  assistance feature to save energy or time.
- Nothing you produce is a safety determination. If your answer could be read as
  clearing the vehicle as safe to drive, say plainly that it is not that.
- Every figure you give is an estimate from what the driver typed in. Say so if it
  matters.

Units and signs:
- Use the same units the driver used, consistently. Don't switch between miles and
  kilometres mid-answer.
- Costs and penalties are NEGATIVE. Gains are POSITIVE. Never flip this — a penalty
  written as a positive number makes range figures improve when the truck gets
  heavier, and nobody will spot it.
```

## Why that last paragraph matters more than it looks

Sign convention looks like housekeeping. It's the failure most likely to survive
unnoticed.

One agent reporting an efficiency penalty as `+18%` instead of `−18%` produces range
estimates that *improve* when you hitch up a trailer. Nothing breaks, no error appears,
and every number still looks plausible. Then you run out of charge 60 km short.

If a figure ever moves in the direction that flatters you when you add weight, stop and
check the signs.

## The "for the Chief" escape hatch

An agent that spots something real outside its scope has three options, and two are
bad:

1. Stay silent — you lose real information.
2. Act on it — scope discipline goes, and with it your ability to tell which agent was
   wrong.
3. **One line marked "for the Chief", and no action.**

RANGE noticing that your consumption history looks like a dragging brake belongs in
option 3. It's SERVICE's call, not RANGE's — but nobody should have to lose the
observation to keep the boundary.

---

# Part 2 · Rules for running it

## 1 · Run them in order. Every time.

```
LOAD → ROUTE → RANGE → CHARGE → CABIN → SERVICE → GUARD → POWERBANK → CHIEF
```

The order isn't a style choice. Each arrow is one agent needing the previous answer:

- ROUTE needs LOAD, because the right road at 3,300 kg is the wrong road at 5,400 kg.
- RANGE needs ROUTE, because average consumption across a route with 2,700 m of climb
  is meaningless.
- CHARGE needs RANGE, because "do I need a stop" is unanswerable without an honest
  arrival figure.
- CABIN needs CHARGE, because comfort spends whatever's left after feasibility.

Running them in parallel to save time is the single most common way people break this
setup. It feels like a free win and it removes every dependency that makes the chain
better than one long prompt.

## 2 · Give each agent only what it asks for

Each agent file lists what it needs. Paste that, nothing more.

It's tempting to give every agent the whole picture. Don't. An agent that can see
everything starts answering questions outside its scope, and narrow scope is exactly
where your ability to catch errors comes from.

## 3 · Respect the three stopping points

| When | If | Then |
| --- | --- | --- |
| After LOAD | you're over a rated limit | stop. Go straight to CHIEF. Don't run the rest. |
| After SERVICE | it vetoed | stop. The veto is the whole answer. |
| After RANGE | the worst case is tight | tell CABIN and POWERBANK their proposals are conditional |

An agent that flags one of these and a driver who carries on anyway has thrown away
the part that protects them.

## 4 · The veto is absolute

SERVICE can stop the trip. CHIEF OF STAFF cannot overrule it, and neither should you
without looking at the thing it named with your own eyes.

The reason the veto sits in its own agent is that every other agent wants your trip to
work. Give the veto to one of them and it will never be used.

## 5 · One sentence out, or it failed

If CHIEF OF STAFF hands you eight findings in a tidy list, it failed — and re-running
it usually fixes it. You already had nine numbers on a screen. A longer list is worse.

**Right:**

> Leave at 14:20, charge 18 minutes at Green River, you arrive with 62 km.

**Wrong:**

> LOAD reports 5,389 kg, ROUTE suggests the valley road, RANGE expects 18%…

If it keeps summarising, check you pasted the "no agent names in the line" rule. That
one constraint fixes it most of the time, because a model that can't cite its sources
has to commit to an answer instead.

## 6 · Don't add a tenth agent without a boundary

Nine isn't sacred. But before adding one, answer this: **what does it own that no
existing agent owns?**

An agent that overlaps an existing one makes the chain worse, not richer — now two
agents produce the same figure and you don't know which to trust. If you can't name
what it exclusively owns, it belongs inside an existing agent as another rule.

## 7 · Check the sentence against your dash

Every run, for the first week. This is nine agents reasoning over what you typed in,
not a reading off your vehicle. If the sentence and your screen disagree, your screen
wins and something in the template is wrong.

## 8 · Fix prompts with a reason, not a vibe

Every rule in these prompts is there because of something that went wrong. Some look
redundant and aren't.

If an agent misbehaves, note the input that caused it before you change the prompt.
"It drifted into another agent's job on this specific trip" leads to a fix. "It felt
off" leads to a prompt that's 40% longer and no better.
