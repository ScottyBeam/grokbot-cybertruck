# Contributing

This is a prompt pack. There's no code, so the useful contributions aren't code either.

## What helps most

**1 · A prompt fix with a reproduction.** The single most valuable thing. Tell me what
the agent did wrong, the input that caused it, and what it does with your fix. A diff
with no reproduction is hard to judge and will sit.

**2 · A new failure mode.** Every agent file ends with a **When it goes wrong** table.
Those tables are the most-read part of the repo. If you hit something not in one, that's
worth an issue on its own even without a fix.

**3 · Vehicle profiles for other cars.** Nothing here is Cybertruck-specific. Filled-in
profiles for other electric vehicles make it immediately usable for more people.

**4 · Translations.** Especially the agent prompts. Someone running these in their own
language shouldn't have to translate nine prompts first.

**5 · Real-world results.** Ran it for a week and compared against your dash? That's
the thing this repo has least of and needs most. Where was it close, where was it off,
and by how much.

## What I won't merge

**Code.** No API clients, no scripts, no orchestration frameworks. The no-code property
is the reason someone can use this ten minutes after finding it, and it's worth more
than any convenience code would add. If you want to automate it, fork it — link your
fork in an issue and I'll point people at it.

**Anything asking for vehicle credentials.** A repository that asks for vehicle tokens
is a repository that eventually leaks someone's vehicle tokens.

**Manufacturer branding.** No logos, wordmarks, or styling implying an official
relationship. See [NOTICE.md](NOTICE.md).

**A tenth agent without a boundary argument.** Nine isn't sacred, but a new agent needs
a clear answer to *what does this own that no existing agent owns?* Two agents producing
the same figure is worse than one, because now you don't know which to trust. If you
can't name what it exclusively owns, it belongs inside an existing agent as one more
rule.

**Parallelising the first four agents.** It looks like a free speed win and it silently
removes every dependency that makes this better than a single prompt. If speed is the
problem, cache LOAD and SERVICE instead — [SETUP.md](SETUP.md) covers it.

**Performance claims.** No accuracy figures, no "improves range by X%", no benchmark
numbers unless the method is in the repo and anyone can reproduce it. Nothing here has
been validated against real outcomes and the README says so plainly. That honesty is
load-bearing.

**Safety-relevant advice.** Nothing that could read as clearing a vehicle as safe to
drive, or that recommends disabling a safety, defrost or demist function.

## If you're changing a prompt

Every rule in these prompts is there because something went wrong. Some look redundant
and aren't — the line telling LOAD not to compare combined weight against axle ratings
looks like pedantry until it tells you you're overloaded with a 1,500 kg trailer.

So a prompt change should say:

1. **What the agent did wrong** — with the input that produced it.
2. **What it does now** — same input, your version.
3. **What else you checked** — a new rule usually affects cases you weren't aiming at.

Shorter is better, but not at the cost of a rule that's holding something up. If you're
deleting a line, say what you think it was protecting against.

## Style

- **No code, anywhere.** Prompts in plain text blocks, examples in plain language.
- Keep agent files in the existing shape: what it does, why it exists, what it owns,
  what it must never do, setup prompt, what to feed it, what it gives back, when it
  goes wrong.
- Write the failure table. It's the section people actually use.
- Prompts are instructions to a model, so write them as instructions: short
  imperatives, one rule per line, and say *why* on the rules that look arbitrary —
  models follow a reasoned rule more reliably than a bare one.
- Costs and penalties are negative, gains are positive. Everywhere. See
  [RULES.md](RULES.md).

## Issues

Include the input that caused it. For a prompt problem, the filled-in template is
usually enough — with anything personal stripped out.
