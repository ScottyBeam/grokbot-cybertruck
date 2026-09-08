# Setup

About ten minutes once. After that, a run takes two or three.

## What you need

- Grok Bot
- Your truck's empty weight and its axle and towing ratings — from the manual or the
  door sticker. You look these up once, ever.
- Five minutes of your own numbers off your screen

That's it. There's nothing to install and nothing to connect.

---

## Step 1 · Create nine agents

In Grok Bot, create nine agents. Name them exactly:

```
LOAD
ROUTE
RANGE
CHARGE
CABIN
SERVICE
GUARD
POWERBANK
CHIEF OF STAFF
```

Use these exact names. They show up inside the prompts — agents refer to each other by
name — and renaming them means editing prompts you'd otherwise never touch.

## Step 2 · Paste the shared rulebook into all nine

Open [RULES.md](RULES.md) and copy the block in **Part 1**. Paste it into each of the
nine agents, at the top of their instructions.

Don't skip this because it's repetitive. It carries the honesty rules, the safety
rules, and the sign convention. Nine agents that don't share a rulebook drift apart
within about a week, and the drift is hard to spot because each one still sounds
reasonable on its own.

## Step 3 · Paste each agent's own prompt underneath

Open each file in [`agents/`](agents/), find the **Setup prompt** block, copy it, paste
it into that agent below the shared rules.

**Copy it exactly.** Every rule in there is a response to something that went wrong.
Some look redundant and aren't — the line telling LOAD not to compare combined weight
against axle ratings looks like pedantry until it tells you you're overloaded with a
1,500 kg trailer.

Nine files, nine pastes. This is the bulk of the ten minutes.

## Step 4 · Fill in your truck's fixed numbers

Open [TEMPLATES.md](TEMPLATES.md), take the **Vehicle profile**, and fill it in once:
empty weight, axle ratings, towing rating, battery size, your usual empty consumption.

Save it somewhere you can copy from — a note on your phone is fine. You'll paste it
into LOAD at the start of every run and never edit it again.

## Step 5 · Do a first run

Take the **Trip template** from [TEMPLATES.md](TEMPLATES.md), fill it in for a drive
you're actually about to do, and go through the chain:

```
1. LOAD          ← vehicle profile + what you're carrying
2. ROUTE         ← LOAD's answer + destination
3. RANGE         ← LOAD's answer + ROUTE's answer + charge level
4. CHARGE        ← RANGE's answer + ROUTE's answer + your arrival floor
5. CABIN         ← RANGE's answer + CHARGE's answer + outside temp
6. SERVICE       ← LOAD, ROUTE, CHARGE + any warnings and wear figures
7. GUARD         ← only if it's been parked somewhere; skip otherwise
8. POWERBANK     ← only if you're running something off it; skip otherwise
9. CHIEF OF STAFF ← all eight answers, pasted together
```

Copy each answer forward as you go. Skip 7 and 8 when they don't apply — most trips
they don't, and the Chief handles missing reports fine as long as you say which are
missing.

**Three places to stop early:**

- LOAD says you're over a rated limit → stop, go straight to CHIEF
- SERVICE vetoes → stop, the veto is the answer
- RANGE flags a tight worst case → tell CABIN and POWERBANK their proposals are
  conditional

## Step 6 · Check it against your dash

Run it for a week alongside what your truck actually tells you.

This is nine agents reasoning over numbers you typed in, not a reading off the vehicle.
If the sentence and your screen disagree, your screen wins — and something in your
template is probably wrong. Usually it's the consumption figure or the battery health.

---

## The two-minute version

If ten minutes is ten minutes too many, [TEMPLATES.md](TEMPLATES.md) has a **one-shot
prompt** — one block that plays all nine agents in sequence inside a single chat.

It's weaker than the real chain, and for an honest reason: one pass lets the model see
all nine jobs at once, which is exactly what the nine-agent split is designed to
prevent. The agent that's supposed to say no can see that everyone else wants the trip
to work.

But it takes two minutes and you'll immediately see whether this is worth your ten.

---

## Make it faster once it's working

**Don't re-run LOAD and SERVICE every time.** They change on a timescale of days, not
trips. Run LOAD when your load actually changes; run SERVICE weekly, or when a warning
appears. Keep their last answers and paste them in.

**Skip GUARD and POWERBANK unless they apply.** Most trips they don't.

**Save your vehicle profile as a snippet.** It's the same text every run.

**Give CHIEF OF STAFF your best model.** It's doing the only genuinely hard work in the
chain — the other eight mostly extract and calculate. If you're choosing where to
spend, spend it there.

## When output goes bad

| What's happening | Look at |
| --- | --- |
| The Chief hands you a summary instead of a decision | [09-chief-of-staff.md](agents/09-chief-of-staff.md) — paste the precedence list verbatim, don't paraphrase it |
| Range figures look too confident | [03-range.md](agents/03-range.md) — expected and worst case must be different numbers |
| Comfort winning when range is tight | the precedence list is missing from the Chief's prompt |
| An agent answering outside its job | you gave it more context than its file asks for |
| Numbers improving when you add weight | sign convention — see [RULES.md](RULES.md) |
| It says you're overloaded with a modest trailer | LOAD is comparing combined weight to axle ratings; re-paste its prompt whole |
