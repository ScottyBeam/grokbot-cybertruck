# Templates

Copy, fill in, paste. Four templates: your vehicle once, a trip each time, a morning
check-in, and a one-shot version for when you don't have ten minutes.

Leave a line blank if you don't know it. **Don't guess** — the agents handle a stated
unknown correctly and handle a wrong number silently.

---

## 1 · Vehicle profile

Fill this in once. Paste it at the start of every run and never edit it again.

```text
VEHICLE PROFILE

Empty weight:            ____ kg
Front axle rating:       ____ kg
Rear axle rating:        ____ kg
Max towing:              ____ kg
Battery size:            ____ kWh
Battery health:          ____ %          (from your service menu, or leave blank)
Usual consumption empty: ____ Wh/km      (from your trip meter)
Units I use:             km / kWh / kg / °C
```

Where to find them: axle and towing ratings are on the driver's door sticker or in the
manual. Consumption is on your trip meter — take a long-run average, not the last five
minutes.

---

## 2 · Trip template

The main one. Takes about a minute off your own screen.

```text
TRIP

Where I'm going:      ____________________
Leaving:              ____________________
Charge now:           ____ %
Minimum on arrival:   ____ %              (15% is a sane default)
Outside temperature:  ____ °C
Plugged in right now: yes / no
People aboard:        ____

CARRYING
In the bed, roughly:  ____ kg
Trailer:              ____ kg             (blank if none)
Tyre pressures:       ____ / ____ / ____ / ____ kPa

HOW I DRIVE
Style:                gentle / normal / quick
Recent consumption:   ____ Wh/km

CHARGING AT HOME
Rate:                 ____ per kWh
Off-peak window:      ____ to ____
Flexible on departure: yes / no

VEHICLE CONDITION                          (for SERVICE)
Warnings showing now: ____________________
Brake pads:           ____ mm             (blank if unknown)
Tyre tread:           ____ mm
Pending update:       ____ , waiting ____ days
Inspection due:       ____________________
Odometer:             ____ km
Last serviced:        ____ km

IF IT'S BEEN PARKED                        (for GUARD — skip otherwise)
Parked for:           ____ hours, at home / street / car park
Sentry events:        ____________________
Next drive:           ____ , ____ km away

IF I'M RUNNING SOMETHING OFF IT            (for POWERBANK — skip otherwise)
Loads:                ____________________  (name, roughly kW, how often it's on)
Must not be cut:      ____________________
Minimum charge floor: ____ %
```

---

## 3 · Morning check-in

The 30-second version, for a normal day with no trip to plan. Give it to CHIEF OF STAFF
directly.

```text
MORNING CHECK

Charge:               ____ %
Parked overnight:     at home / street / car park
Sentry events:        ____________________
Outside now:          ____ °C
Plugged in:           yes / no
Driving today:        ____ km, leaving around ____
Warnings showing:     ____________________

One sentence. If nothing needs doing, say so in under twelve words.
```

That last line matters. On most mornings the correct output is something like *"Cabin
will be warm at 07:29 and it costs you nothing — leave whenever."* An assistant that
finds something to say every morning is one you stop reading by the second week.

---

## 4 · One-shot prompt

All nine in a single chat. Weaker than the real chain — one pass lets the model see
every job at once, which is what the nine-way split exists to prevent — but it takes
two minutes and shows you the shape.

Paste this, then paste your vehicle profile and trip template under it.

```text
Act as nine separate agents in sequence, then as their chief. Work through them in
order. Don't skip ahead, and don't let a later agent's knowledge leak into an earlier
one's answer. Keep each of the first eight to a few lines.

1 LOAD — establish what's being carried. Report combined weight (truck + trailer,
  this drives energy) separately from vehicle-only weight (truck + payload + tongue,
  the ONLY figure compared against axle ratings — a trailer carries its own mass on
  its own wheels). Give the front/rear split, one efficiency penalty as a negative
  percentage, and tyre pressure judged against the target for this load. If any rating
  is exceeded, say so first and stop the whole run here.

2 ROUTE — using LOAD's combined weight, give the road as segments with climb, surface
  and current weather. Under load, prefer less climb over less distance and say what
  that trade is worth. Mark any unknown gradient as unknown, never as flat. Say how
  current your conditions data is. Finish with specific things that would make this
  route wrong.

3 RANGE — using LOAD's penalty and ROUTE's segments, work out arrival charge segment by
  segment, never with one average. Give expected AND worst case; THE WORST CASE IS THE
  HEADLINE. Name each correction's contribution: mass, climb, temperature, wind,
  altitude, style. Scale capacity by battery health, not nameplate. Flag if the worst
  case is under 10%.

4 CHARGE — FIRST answer whether a stop is needed at all, by comparing RANGE's worst
  case to the minimum arrival figure. If it clears, say "no stop needed" and move on.
  If not, pick a real site on minutes, money and risk together; name the runner-up and
  why you rejected it; say what charge level you're targeting and why (charging slows
  as it fills); price home overnight against it. Detours justify themselves in minutes,
  not distance. Never say a charger "is available" — say when it was last reported.

5 CABIN — price comfort in kilometres. If plugged in, preheat from the wall at zero
  range cost — this is the most valuable thing here. State the exchange rate in km per
  degree. Scale by how many people are aboard. Demist load is fixed, not optional.
  Never suggest disabling defrost or demist. Mark proposals conditional if RANGE
  flagged a tight worst case.

6 SERVICE — you hold a veto. Veto ONLY for an active serious fault, a wear item past a
  safety or legal limit, or a legal date expiring during the trip — and name it. If you
  veto, that's the entire output. Otherwise report wear as a trend plus where it's
  heading, and split findings into MUST HAPPEN BEFORE THIS TRIP and SHOULD HAPPEN THIS
  MONTH — never merged. Fit any pending update into the charge stop if it fits.
  Consider what this specific route does to the vehicle.

7 GUARD — report only contact, a door or port interaction, glass, or someone staying
  close for over thirty seconds. Transient motion never qualifies. NEVER describe or
  identify a person — sensor facts only. Timestamp and duration on each event, and say
  how many triggers you filtered out as a number. Work out what the watch cost against
  the next drive. "Quiet" is a complete and common answer.

8 POWERBANK — work out the reserve FIRST: next committed trip's energy, plus the
  overnight watch, plus the driver's floor. Energy below that doesn't exist. Every
  runtime names the reserve it stops at, never empty. Publish a shed order; never shed
  a must-not-interrupt load. Apply export efficiency.

9 CHIEF OF STAFF — read all eight. Do NOT forward them. Settle conflicts strictly in
  this order: SERVICE's veto, LOAD's limits, RANGE's worst case, CHARGE's decision,
  POWERBANK's reserve, SERVICE's must-dos, GUARD's events, CABIN's comfort. Never
  average two conflicting numbers — name the winner. Then output exactly:

  THE LINE  — ONE sentence: a decision, a time or place, and a number. No agent names,
              no lists, no hedging.
  BECAUSE   — at most two short clauses.
  DECIDE    — nothing, or ONE question. Never two.
  RESOLVED  — the conflicts you settled, one line each.
  DROPPED   — what you chose not to mention.

All nine: never fill a missing number with a typical value — say it's missing and be
less certain. Never round in the favourable direction. Costs and penalties are
negative. Never suggest disabling a safety, defrost or demist function. Nothing you
produce is a safety determination. Every figure is an estimate from what I typed in.

My numbers follow.
```

### What to look for in the answer

One question decides whether it worked: **is the last line a decision, or a summary?**

Good:

> Leave at 14:20, charge 18 minutes at Green River, you arrive with 62 km.

Failed:

> LOAD reports 5,389 kg, ROUTE suggests the valley road, RANGE expects 18% at
> arrival…

Same facts. Only one is something you can act on. If you're getting the second one,
that's the exact problem the nine-agent version fixes — see [SETUP.md](SETUP.md).
