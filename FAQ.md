# FAQ

## Does this actually connect to my truck?

No. There's no app, no API, no login, nothing to authorise.

You paste the numbers your own screen is already showing — that's what the trip
template in [TEMPLATES.md](TEMPLATES.md) is for. Takes about a minute.

That sounds like a limitation and it's partly a feature: it works today, on any
vehicle, and you never hand your vehicle credentials to anything.

## Then why is it better than just looking at my dash?

Your dash shows you nine numbers. It doesn't tell you which one wins.

Your range says 18%. Your comfort setting costs something you can't see. There's a
software update pending. A charging stop exists somewhere ahead. Every one of those
facts is on a screen somewhere and none of them are a decision.

That's the actual work: reading nine facts that disagree with each other and producing
one instruction. See the conflict table in
[09-chief-of-staff.md](agents/09-chief-of-staff.md) — five agents, all correct, and a
decision none of them can make on their own.

## Why nine agents instead of one good prompt?

Try one prompt first. It works, sort of, and it fails in three specific ways.

**It hides its mistakes.** When one prompt says "you'll arrive with 18%", you can't
tell whether the weight was wrong, the climb was wrong, or the arithmetic was wrong.
Nine narrow agents each give you a checkable answer, so a mistake is attributable — and
attributable mistakes get fixed.

**It lets convenience win.** A model asked to plan a trip wants the trip to work. Ask
it whether the tyre at 1.4 mm is a problem and it will find a reason it's fine. Putting
the veto in its own agent, whose only job is to say no, is a structural fix for a
behavioural problem. You can't prompt your way out of it in one pass.

**It can't be ordered.** RANGE needs LOAD's answer. CHARGE needs RANGE's. In one prompt
those dependencies are implicit, so they're sometimes honoured and sometimes not — and
you can't tell which run you got.

## Is nine agents overkill for a school run?

Yes, and that's the point of the [morning check-in template](TEMPLATES.md) — 30
seconds, straight to the Chief.

On a normal day the correct output is something like *"Cabin will be warm at 07:29 and
it costs you nothing — leave whenever."* One useful sentence. The full chain earns its
keep on the trips where something is actually tight: cold, loaded, long, or all three.

## Do I have to run all nine every time?

No. Two shortcuts that don't break anything:

- **Skip GUARD and POWERBANK** unless they apply. Most trips they don't.
- **Don't re-run LOAD and SERVICE every trip.** They change on a timescale of days.
  Run LOAD when your load changes, SERVICE weekly or when a warning appears, and paste
  their last answers forward.

What you can't skip is the order, or the Chief. See [RULES.md](RULES.md).

## Can I run them in parallel to save time?

This is the most common way people break the setup, so: no, not the first four.

RANGE genuinely needs LOAD's number. CHARGE genuinely needs RANGE's. Running them
simultaneously means each one is guessing at the input it should have been given, and
you've quietly rebuilt the single-prompt version with extra steps.

Agents 6, 7 and 8 can run whenever. 1 through 4 cannot.

## What if I don't know some of the numbers?

Leave them blank. **Don't guess.**

The agents are built to handle a stated unknown — they say what's missing and lower
their confidence. What they can't handle is a wrong number, because nothing about it
looks wrong. A blank line costs you a little certainty. A wrong tyre pressure costs you
a bad answer you'll believe.

## Which model should I use?

Give **CHIEF OF STAFF** the best one you have access to. It does the only genuinely
hard reasoning in the chain — the other eight mostly extract and calculate.

RANGE and CHARGE benefit from a strong model too, since their corrections compound.
CABIN, GUARD and POWERBANK are mechanical enough that a smaller one is fine.

## Does it work for cars that aren't a Cybertruck?

Yes. Nothing here is model-specific — it's built around weight, climb, cold, wind and
charge, which every electric vehicle has.

Fill in your own numbers in the vehicle profile. POWERBANK only makes sense if your car
can export power, and LOAD's trailer handling only matters if you tow, so drop what
doesn't apply.

## Can I add a tenth agent?

Nine isn't sacred. But answer one question first: **what does it own that no existing
agent owns?**

If it overlaps an existing agent, you now have two agents producing the same figure and
no way to know which to trust — that's worse, not richer. If you can't name what it
exclusively owns, it belongs inside an existing agent as one more rule.
[CONTRIBUTING.md](CONTRIBUTING.md) has more.

## Has any of this been tested against real drives?

Not systematically, and I'd rather say so than imply otherwise.

No arrival prediction in this repo has been checked against an actual arrival over a
meaningful number of trips. "Honest figure" here means *honestly worked out from what
you typed in*, not *verified accurate*. The design makes errors visible and
attributable; it doesn't make them small.

That's exactly why [SETUP.md](SETUP.md) tells you to run it against your dash for a
week before trusting the sentence.

## Can I rely on the SERVICE veto?

No. It's a prompt being careful over numbers you typed in — not an inspection, not a
diagnostic scan.

Treat a veto as a strong hint to go and look at the thing it named with your own eyes.
Treat *no veto* as meaning nothing at all about whether your vehicle is safe. Use your
instruments, the manufacturer's guidance, and your own judgement for anything that
matters.

## Is this affiliated with Tesla or xAI?

No. Independent project, no logos, no endorsement, no relationship. See
[NOTICE.md](NOTICE.md).

## Can I use it commercially / change it / ship my own?

Yes, all three. It's [MIT](LICENSE). Take it, change it, ship your own version. A link
back is appreciated and not required.

## Something's wrong with an agent. What do I do?

Each agent file ends with a **When it goes wrong** table — symptom, cause, fix. Start
there; most problems are one of about five things, and three of them are "the prompt
wasn't pasted whole".

If it's not in the table, open an issue with the input that caused it. See
[CONTRIBUTING.md](CONTRIBUTING.md).
