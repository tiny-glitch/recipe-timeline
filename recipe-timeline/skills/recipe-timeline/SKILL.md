---
name: recipe-timeline
description: "Convert a written recipe into an execution-order cooking timeline: when to act, how long until the next action, where prep fits, and how parallel components converge. Use when a recipe needs turning into a cooking plan."
---

# Recipe Timeline

Turn a conventional recipe into an execution plan for one cook.

A recipe lists actions in **written order**. This skill re-renders them in **execution order** — a cooking dependency graph laid out against a clock.

Each timeline row answers three questions at once:

- **Where am I?** — cumulative position
- **How long until I must act?** — the interval marker
- **Am I free right now?** — the weight of the vertical rule

The underlying rule for prep:

> as late as practical, and as early as necessary

---

# 0. Route First: Mode and Fit

Before anything else, classify the recipe. Choosing the wrong mode is the most common failure.

**If the recipe has fewer than 5 actions and no meaningful timing or parallelism** — scrambled eggs, a salad, a smoothie — recommend a simple numbered sequence rather than a full timeline, and say in one line why the timeline would be overhead. **If the user explicitly asks for the timeline format, give them the timeline.** This threshold is a default, not a refusal.

Otherwise pick a mode:

## Mode A — Timeline (default)

At least one interval over 3 minutes and 6+ actions. Most stovetop mains, braises, stews, curries, pasta, sheet-pan dinners.

## Mode B — Sequence (attention-dominant)

**Most intervals under 90 seconds**: stir-fries, omelettes, tempering-heavy dishes, fast sautés — anything where the cook never leaves the pan.

There is no free time in Mode B, so do not pretend there is. Output:

1. **Mise en place, in reach order** — everything prepped and arranged before heat, listed in the order it will be grabbed.
2. **A before-heat block** carrying any preheat gate and its lead time.
3. **A fast sequence** with cumulative seconds and gates, no `[+n]` markers and no prep tasks inside it.
4. An explicit line: *"From heat on to plate is about N minutes with no pause. Everything above must be within arm's reach first."*

Mode B is the exception to "do not dump all mise en place before the clock." Here, dumping it there is correct.

## Mode C — Schedule (wait-dominant)

Active work is a small fraction of elapsed time: baking, bread, chilled and set desserts, fermentation, sous vide, overnight braises, anything with a multi-hour tail. Output **sessions anchored backward from serve time**, not a continuous clock from zero.

## Mixed

Many recipes are Mode A with a Mode C tail — banana bread is a 20-minute timeline plus 70 minutes of cooling. Render the timeline, then an **After active cooking** block. Do not mark the dish complete until the tail is done.

## Revising an existing timeline

When the user changes a recipe that already has a timeline — adds a side, drops a component, sets a serve time, doubles the quantity — **re-run the whole skill from the original source. Do not patch the previous output.**

A timeline is a graph, not a list. One new component changes the merged action list, so markers downstream shift; it can also create a collision, consume the last free prep window, put a second pot on a burner, exceed the pot's capacity, or push the meal past two lanes and into serve-time anchoring. Local edits are exactly how a timeline ends up internally inconsistent, with stale markers pointing at actions that have moved.

Re-running is cheap. What is not cheap is losing what the previous pass established, so carry it forward explicitly under a **Carried forward** heading above the new plan:

- methods the user supplied that the source did not contain
- a serve time, serving count or container count they gave
- corrections they made to the last output
- anything they said they do differently

Stating them makes them correctable rather than silently assumed.

Then, beneath the new timeline, add a short **What moved** list — only what changed position or attention, not a recount:

```text
What moved
- Garlic 9 min → 11 min (pasta pot now starts at 4 min)
- Collision at 22 min: drain moved into the spinach wilt
- Two hot components now, so the plan anchors to serve
```

The user may already have the old plan in their head. Telling them what shifted is the point of re-running rather than patching.

---

# 1. Source Gate

If ingredients are present but the **method** is missing, stop. Report that a faithful timeline cannot be built. Do not invent a plausible recipe.

If the method is present but **times are missing** (very common on recipe blogs and in traditional recipes), proceed — but mark inferred durations per §7 and lead every cue-driven step with the cue, not the number.

---

# 2. Extract

From the source, capture: title, servings, ingredients, instructions, stated times, temperatures, equipment, doneness cues, accompaniments, and any stated nutrition figures.

Do not alter ingredient quantities.

If the user asks to scale the recipe, note that durations do not scale linearly — a doubled quantity browns slower, may need a larger pan, or may force batches (§7, repeat cycles).

---

# 3. Nutrition Block

Every output opens with servings, energy and macros, before anything else. The cook decides whether to make the dish from this block, so it goes above the equipment and ingredients.

```text
Serves 4  ·  Per serve ~300 kcal  ·  Total ~1,200 kcal
P 13 g  ·  C 42 g  ·  F 9 g                     [est.]
```

Protein, carbohydrate and fat are the default three. Carry fibre, sugar or sodium only when the source gives them. Always show **both** per-serve and total.

## Three tiers, always marked

`[source]` — the recipe states the figures. Quote them verbatim. Do not recompute them, and do not "correct" them even if your own estimate disagrees; say so in a line beneath if the gap is large.

`[est.]` — computed by you from the ingredient list. Never present these as the recipe's own numbers.

**Omitted** — the ingredients cannot support an estimate. Show what you can and name the blocker:

```text
Serves 4  ·  Energy not estimated
"Oil for deep frying" — no quantity, and absorbed oil
dominates the figure.
```

This mirrors the `[+]` / `[≈]` distinction used for durations. Same rule: an invented number rendered in the typography of a sourced one is a fidelity failure.

If the source contradicts itself, point at the contradiction and say which figure matches the recipe as written. Do not silently pick one:

```text
Source note: the ingredient list says 20–30 g parmesan
but its second table is calculated on 66 g.
```

## Servings

Per-serve figures need a serving count.

- Stated by the source → use it. A storage line like "portion into 6 containers" states it.
- Not stated → infer from total quantity and mark it: `Serves ~6 [est. from volume]`.
- Cannot be inferred → show totals only and say per-serve needs a serving count.

When the source gives per-serve figures only, multiplying out for the total is arithmetic on its numbers, not a new estimate. It stays `[source]`.

## Estimating honestly

These are what make ingredient-list estimates wrong. Each has a rule:

- **Frying oil** — only the absorbed fraction counts, not the pan-full. Shallow and deep frying absorb roughly 10% of the oil by weight of food. If the recipe says "oil for frying" with no quantity, that defeats the estimate — omit rather than guess.
- **Discarded marinade** — count roughly a quarter, what clings to the food.
- **Brines and salted boiling water** — mostly discarded. Ignore unless the source tracks sodium.
- **"Salt to taste", "oil as needed"** — count as zero and say so.
- **Trim, bones, shells, peel** — estimate on edible yield, not purchase weight.
- **Reduction** — evaporation removes water, not energy. A reduced sauce has the same calories as before it reduced. Never scale the figure down for simmering.
- **Alcohol** — assume roughly half retained in a simmered dish, nearly all in an unheated one.
- **Accompaniments not costed in the recipe** — exclude them, and say the figure is for the dish alone: *"Rajma only — rice adds ~205 kcal per serve."*

## Precision discipline

An ingredient-list estimate is worth ±15% at best. Never render it more precisely than that supports: `~520 kcal`, not `518 kcal`. Round energy to the nearest 10 kcal and macros to the nearest gram. Do not put ranges on macros — round hard and let `[est.]` carry the uncertainty. Ranges the **source** gives are preserved as written.

## Scope

Report the arithmetic and stop. Do not comment on whether the figures are high or low, suggest substitutions to change them, or attach dietary advice — none of that is what this skill was asked for. Drop any evaluative language the source itself carries ("very filling", "moderate saturated fat") and keep the numbers.

Do not produce estimates for a recipe already failed under the Source Gate. If the method is not trustworthy, the numbers are not either.

---

# 4. Expand Implicit Prep

Recipes omit obvious execution steps. For every action, ask:

> What physical state must this ingredient be in when this action happens?

`ADD ONION` → onion must be chopped → create the prep task.
`ADD TOMATO PURÉE` but source lists whole tomatoes → create: purée tomatoes.
`ADD SPICES` with five spices listed → create: measure spices.

Common legitimate additions: chop or slice aromatics; mince or grate ginger and garlic; drain and rinse tinned goods; wash rice; measure spices; cut citrus; chop herbs; open packets; get frozen items out; put a vessel in place before a pour or a drain; bring dairy or eggs to room temperature when the method depends on it.

## Execution inference vs culinary invention

**Allowed** — inferring preparation requirements that the method presupposes.

**Not allowed** — adding ingredients not in the source; changing chopped to puréed when the source says chopped; shortening stated times; changing temperatures or techniques; substituting.

Infer preparation. Do not rewrite the dish.

---

# 5. Build the Graph

## Spine

One primary process: the one with the most sequential dependencies, needing the most intervention, that determines when everything else starts.

Processes are **not** separate components merely because they run concurrently or in different vessels. Cooking beans, making their masala, and combining them are one dish, one lane.

## Branches, and where their methods come from

A branch is a genuinely separate meal component: rice, pasta, potatoes, a vegetable side, a raita, a sauce made separately. Start it **at the point it should actually begin**, not at Clock 0.

**A branch requires a method.** A serving suggestion is not a branch:

```text
Source says only "serve with rice" — no method given:

  ### Serve with
  Rice — method not given.

User supplies one — branch allowed, and credited:

  Rice method supplied by you, not the source:
  wash · boil · low + cover 12 min

  10 min   ├············→ 🔪 Wash rice · pot on
```

A method may come from the source, from a second recipe, or from the user. When it comes from the user, say so in the output so the reader can see where those steps originated. Never manufacture one.

## Gates and stop-conditions

Conditions, not durations. Never proceed on the clock alone.

`◆` **proceed when true** — `◆ until oil shimmers`, `◆ oven at 200°C`, `◆ water at a rolling boil`, `◆ dough doubled`, `◆ skewer comes out clean`

`◇` **stop as soon as true** — `◇ just combined`, `◇ first sign of colour`, `◇ slightly underdone`. Opposite polarity to `◆`, and confusing the two ruins batters, custards and emulsions.

`🌡` **internal temperature** — use whenever the source gives one. A gate, never a sensory cue; it overrides the clock absolutely.

A gate also covers a **setup precondition** that makes a step irreversible if missed. Put it on the line above the action, not inside it:

```text
            │               ◆ CUP BY THE SINK
23 min      │               ● RESERVE 1 CUP · DRAIN
```

## Carryover cooking

Roasts, thick cuts and large bakes keep cooking after leaving the heat — typically 3–5°C for a joint or bird, more for a very large one. When the source gives a finished temperature, pull at that figure minus the carryover and say so:

```text
🌡 pull at 71°C — rises to ~75°C while resting
```

## Equipment

Respect shared pans, burners, oven space, blenders. Two cases, and they are different:

**Sharing** — both fit at once at the same temperature. Note the load and move on.

**Contention** — one at a time, or conflicting temperatures. Resolve in this order:

1. **Within ~15°C** — use the higher temperature and pull the more delicate item earlier by checking its gate sooner.
2. **Further apart** — sequence them. The item with the longer hold tolerance goes first and waits: roast meat rests happily for 20 minutes while potatoes crisp at a higher heat.
3. **Neither works** — state the conflict and offer the choice. Do not silently pick one.

Also flag **capacity**: sum the finished volume and say when a recipe needs a bigger pot than its wording implies, and say when two burners must run at once and from when.

## Preheat and come-to-temperature lead times

Almost never stated, and the most common real-world timing failure. Plan with:

- home oven to 180°C: ~15 min; to 220°C: ~20 min
- oven with stone or steel: add 20–30 min
- grill / broiler: ~5 min
- large pot of water to a boil: ~8–12 min
- oil for deep frying to 180°C: ~8–10 min
- wok to smoking: ~2–4 min
- pan to medium for sautéing: ~1–2 min

Put the preheat on the plan early enough that the gate is met when needed. In Mode B it belongs in **Before heat**, outside the sequence clock.

---

# 6. The Time Model

Every task lands in exactly one zone.

## EARLIER (long-lead)

A task belongs here when **it forces a wait you cannot fill with the rest of the prep** — soaking, proving, fermenting, thawing, curing, a long marinade. Judge behaviourally, not by a fixed threshold: a 20-minute marinade that runs while you prep everything else is pre-prep, not long-lead.

Do not draw an eight-hour timeline because something soaked overnight — collapse it to a block.

## BEFORE ACTIVE COOKING (Mode A) / BEFORE HEAT (Mode B)

Mode A: only what genuinely must be ready at Clock 0 — pre-cooked components used later in the main sequence, ingredients needed within the first minute, whole spices that hit hot oil immediately. Do not dump all mise en place here, except when the recipe is a bake (see the baking exception).

Mode B: the full mise en place in reach order, **plus** any preheat gate with its lead time. The sequence clock starts when the food hits the heat, not when the pan does.

## TIMELINE / SEQUENCE / SESSIONS

Clock 0 is the start of the **main continuous execution sequence**, not the first thing ever done to an ingredient. Mode C has no Clock 0 — it uses offsets from serve (§9).

## AFTER ACTIVE COOKING (long-tail)

Resting, cooling, chilling, setting, maturing. Mandatory whenever it exists, and the zone most often dropped.

1. The dish is **not** `✓` until the tail is done. A cheesecake is not finished when the oven goes off.
2. A rest window is **prime working time**, not dead time. Meat resting 15 minutes is when the gravy gets made and the greens get cooked. Schedule into it deliberately.

## Headline numbers

Report both, and never conflate them. Work hidden in EARLIER or BEFORE ACTIVE COOKING belongs in the second number only:

```text
Active ~27 min  ·  Total elapsed ~1 hr 15 + soaking
```

Here 45 minutes of pressure-cooking sits outside the 27-minute clock. A rajma that hides it is not a 27-minute recipe.

---

# 7. Durations

## Marker syntax

- `[+5 min]` — **stated by the source**
- `[≈5 min]` — **inferred by you**
- `[+3–5 min]` — a stated range, preserved

Rendering an invented number in the same typography as a sourced one is a fidelity failure. Include the legend in every output.

**When the source gives no durations at all**, universal `[≈]` marking distinguishes nothing and becomes noise. Drop the per-interval marker and put one line above the timeline instead:

```text
Source gives no times — all intervals below are estimates.
Cook to the cues, not the clock.
```

Then use plain `[+n]` throughout and let the cues carry the weight.

## What a marker measures — the gap to the next ACTION

A marker counts down to the next moment the cook must do something **anywhere in the recipe** — not to the next event in the same lane, and not to the end of whatever is currently cooking.

Compute markers against the **merged** action list of every lane. A branch opening at 4 min splits a ten-minute onion window into `[+4 min]` and `[+5 min]`; it does not stay `[+8–10 min]` because the onion is still going.

## Where a marker sits — immediately below its timestamp

Put the marker on the line **directly under the timestamp it follows**, so reading down the left column gives "here I am / here's how long I've got" with nothing in between. The cooking duration, the cue and any prep go on the lines after.

```text
0 min       ● OIL + ONION, medium heat
[+4 min]    │  cook 8–10 min → soft and golden
            │
4 min       ├············→ ● PASTA POT ON
```

`cook 8–10 min` is the doneness spec; `[+4 min]` is when the cook next has a job. Conflating the two is the most common marker error — invisible in a single-lane recipe, obvious the moment a branch interleaves.

## Threshold — no marker for a gap of 1 minute or less

Skip these entirely. Two consecutive timestamps a minute apart already say everything a `[+1 min]` would. Keep the action's own short duration inline instead:

```text
9 min       ● ADD GARLIC
            ┃  1 min, don't let it colour
10 min      ● ADD PASSATA
```

The left column should carry only the gaps worth planning around.

## Ranges and compounding

Preserve source ranges on the **durations** — never collapse `cook 3–5 min` to `4 min`.

But compute markers and cumulative timestamps along a single nominal path (the midpoint), or a long recipe accumulates into `~95–130 min`, which informs no one. Durations carry the uncertainty; positions and gaps stay single numbers. Prefix inferred positions with `~`.

## Repeat cycles (`↻`)

Batch work — frying in loads, pancakes, dosa, searing in two goes — is one event with a cycle, never N copies:

```text
14 min      ● FRY BATCH 1
[≈20 min]   ╎  ↻ ×4 · ~4 min each + 1 min recovery
            ╎  ◆ hold cooked batches at 100°C
34 min      ● LAST BATCH OUT
```

Always account for recovery time between cycles, and always say where finished batches wait.

## Visual weight

A 25-minute wait must occupy more vertical space than a 2-minute one. Perceptual hierarchy, not proportional scaling.

---

# 8. Scheduling Prep

Do not ask "what can I prep before I start?" Ask:

> What is the latest calm opportunity to prepare this before I need it?

Work backward: what must be ready, how long it takes, the latest usable window, place it there, move it earlier only if forced.

## Attention tiers — and show them

A duration existing does not mean the cook is free. Classify every interval, then **draw it at its weight**:

| Tier | Rule | Prep allowed |
|---|---|---|
| Passive | `│` | Yes — prep and cleanup |
| Lightly attended | `│` | Small tasks only |
| Cyclical | `╎` | Trivially interruptible tasks, between turns |
| Attended | `┃` | None — stay at the pan |

Passive: covered simmer, roasting, resting, chilling. Lightly attended: slowly softening onions, a gentle reduction, greens wilting after one stir. Cyclical: batch frying, flipping pancakes — free in the gaps, committed at the turns. Attended: frequent stirring, tempering, fast browning, rapid sequential additions, emulsifying dairy into a sauce.

The weight of the rule is what lets a cook see at a glance where the free time is. Classifying attention without rendering it wastes the analysis.

## Do not overload

A three-minute window does not hold six tasks. Optimize for low cognitive load, not theoretical efficiency.

## The baking exception

When the recipe is a bake, or when a batter, dough, or emulsion becomes time-sensitive the moment components are combined, **the just-in-time rule inverts**. Full mise en place before Clock 0 is correct technique: gluten develops, leavening starts, emulsions break. Do not schedule flour-weighing into a window after the wet ingredients are mixed.

## Planning estimates

For scheduling only — do not display these:

drain a tin ~1 min · measure spices ~1–2 min · chop onion ~2–4 min · chop capsicum ~2 min · wash rice ~1–2 min · chop tomato ~1–2 min · purée tomato ~2 min · mince garlic ~1–2 min · grate hard cheese ~1–2 min · drain a large pot of pasta ~1 min · garnish ~1–3 min · zest and juice a lemon ~2 min

---

# 9. Convergence: Serve Time, Collisions and Shelf Life

The hard part of a multi-component meal is not when to chop. It is getting everything to the table at once, hot, with one pair of hands.

## Anchor to serve whenever components must converge

If **two or more components must arrive hot together**, anchor to serve — whether or not the user gave a clock time. With a stated time, use it. Without one, use offsets:

```text
T−45   ● Chicken into oven
T−15   ● Chicken out · rest · make gravy
T−5    ● Greens into pan
T−0    ✓ Plate
```

`T−n` is minutes before serve; `T−h:mm` for anything over an hour. Forward-scheduling from Clock 0 cannot express convergence — do not default to it for multi-component meals.

Schedule the shortest-tolerance component last and let the longest-tolerance component absorb the slack.

## Most branches do not merge

A branch that finishes **alongside** the main dish is the common case: rice next to a curry, greens next to a roast. It ends on its own `✓` row and nothing else happens to it.

Reserve `└──→` for components that genuinely **combine into one dish** — pasta going into its sauce, a sauce stirred through. Drawing a merge where the two things simply share a plate is wrong, and it misrepresents a hold tolerance as a deadline.

## Collisions — the cook has one pair of hands

Two lanes may hold events in the same minute. One cook cannot execute both. After placing every event, walk the merged timeline and flag any two hands-on actions within a minute of each other in different lanes.

Resolve by moving the flexible one **into a genuinely passive window of the other**, not by nudging it arbitrarily:

- draining pasta while spinach wilts — fine; the wilt takes one stir and then looks after itself
- draining pasta while dairy is being emulsified into a sauce — a collision; move the drain earlier

If neither can move, say which one waits and what that costs.

## Hold tolerance — finished components

Annotate every completion with how long it will wait:

```text
✓ RICE (holds 20 min covered)
✓ GREENS (holds ~3 min — do last)
✓ ROAST (needs 15 min rest — do first)
```

Rough tolerances: rice covered 20–30 min · braises and stews indefinitely, improving · roast meat 15–25 min resting · mashed potato 20 min covered · pasta ~0, dress immediately · green vegetables 2–5 min · fried food 10 min in a low oven, degrading · anything crisp ~0.

## Shelf life — intermediates

Some things degrade **after you make them and before you use them**. Flag these where they are made, not where they are used:

- leavened batter (pakora, pancake, beer batter): use within ~20 min
- whipped cream and beaten egg whites: fold immediately
- dressed salad, cut avocado, cut apple: minutes
- garlic in oil, cut soft herbs: same session
- melted chocolate, cooked caramel: work while warm

If a batch process outlasts its own batter's shelf life, say so and split the batter.

---

# 10. Visual Grammar and Rendering

```text
●          action requiring attention
│          elapsed — passive, free for prep
╎          elapsed — cyclical, free between turns
┃          elapsed — attended, not free
◆          gate — proceed only when true
◇          stop-condition — stop as soon as true
🌡         internal temperature target
🔪         just-in-time prep
🧹         optional cleanup, passive periods only
↻          repeated batch cycle
[+n]       stated gap to the next action
[≈n]       inferred gap to the next action
[source]   nutrition stated by the recipe
[est.]     nutrition computed from the ingredients
T−n        minutes before serve
├············→  parallel component branch
└────────────→  convergence (components that combine)
✓          component complete
→          sensory cue
```

Put a legend at the top of every output, listing only the symbols actually used.

## Width and rendering

**Always put the plan inside a fenced code block.** That fence is what guarantees monospace; outside one, a proportional font collapses the lanes and the layout becomes meaningless.

**Target ~60 characters per line.** A budget, not a hard cutoff — a line slightly over is fine; a layout that only works at exactly one width is not. Renderers and phone widths wrap differently, so the real rule is: **never build a row whose meaning is lost when it wraps.** Keep each row independently readable and put the important content early in the line.

## Lanes and rows

No column headers — they collapse. A branch runs in a **fixed right-hand column** for the rest of its life, so anything sitting in that column is visibly the same component without needing a header. Open and close it with a long arrow, and keep the lane's `│` running through the rows in between so the reader can follow it down the page.

**Simultaneous events share a row.** Never stack two events carrying the same timestamp on consecutive lines — that reads as a sequence, which is the opposite of what it means.

At most two lanes. If a third component needs one, give it its own block below the timeline and cross-reference it by time.

---

# 11. Output Structure

```
## [Recipe Name]

Serves X · Per serve ~N kcal · Total ~N kcal
P n g · C n g · F n g                    [source|est.]

**Active:** X min · **Total elapsed:** Y

### Carried forward
Only on a revision: methods, serve times and
corrections the user supplied in an earlier pass.

### Equipment
Anything beyond knife, board, and one pan. Flag pot
capacity, and any item or burner needed twice at once.

### Ingredients
Grouped by the moment they are used, not by component,
when that helps the cook stage the bench.

### Serve with
Accompaniments named in the source, without invented
methods. Note their energy separately if known.

### Earlier
Long-lead only, when it exists.

### Before active cooking / Before heat
Only what genuinely must be ready.

### Timeline / Sequence / Schedule
Legend, then the plan.

### After active cooking
Rest, cool, chill, set. Mandatory when it exists.

### What moved
Only on a revision: what shifted since the last plan.

### Storage / reheating
Only if requested or supported by the source.
```

The nutrition block sits above the time headline — it is what decides whether the dish gets cooked at all.

---

# 12. Worked Examples

These are integration tests, not the teaching material — the rules are taught by the snippets beside them. Each shows one mode end to end.

## Mode A — tomato pasta with beans, peas and spinach

```text
Serves 6  ·  Per serve ~540–590 kcal
Total ~3,240–3,540 kcal                      [source]
P 24–27 g · C 75–82 g · F 12–15 g
Fibre 13–16 g · Sat fat 3–4.5 g
Source note: the ingredient list says 20–30 g parmesan
but its second table is calculated on 66 g.

Active ~28 min  ·  Total elapsed ~32 min

Legend  ● act  │ free  ┃ attended  ◆ gate  ◇ stop
        [+n] to next action · 🔪 prep · ✓ done
        Right-hand lane is the pasta.

BEFORE ACTIVE COOKING
● DICE ONION (~3 min)

TIMELINE
0 min       ● OIL + ONION + PINCH SALT, medium
[+4 min]    │  cook 8–10 min → soft, golden, savoury
            │
            │  🔪 Mince garlic
            │
4 min       ├·············→ ● LARGE POT ON, salt well
[+5 min]    │               │  ◆ to a rolling boil
            │               │
            │  🔪 Measure herbs, chilli, pepper
            │               │
9 min       ● ADD GARLIC    │
            ┃  1 min, don't let it colour
10 min      ● PASSATA + HERBS + CHILLI + PEPPER
[+4 min]    │  simmer 8–10 min
            │               │
            │  🔪 Drain + rinse cannellini
            │               │
14 min      │               ● PASTA IN
[+5 min]    │               │  9 min ◇ slightly underdone
            │               │
            │  🔪 Grate parmesan
            │  🔪 Peas, spinach, cottage cheese out
            │               │
19 min      ● BALSAMIC · TASTE · SALT GRADUALLY
            │  → until savoury and rounded
20 min      ● ADD BEANS + FROZEN PEAS
[+2 min]    │  simmer 2–3 min
            │               │
22 min      ● ADD SPINACH · stir once, let it wilt
            │               ◆ CUP BY THE SINK
23 min      │               ● RESERVE 1 CUP · DRAIN
            │               ✓ PASTA (holds ~3 min)
24 min      ● HEAT LOW · COTTAGE CHEESE + PARMESAN
[+2 min]    ┃  + splash of pasta water · DO NOT BOIL
26 min      └─────────────→ ● ADD PASTA · MIX
[+2 min]    ┃  adjust salt, pepper, pasta water
28 min      ✓ DONE
```

This exercises: markers computed across both lanes and sitting under their timestamps; no `[+1 min]`; cooking durations riding on their events; a setup gate before an irreversible step; a `◇` stop-condition; a resolved collision — the drain sits inside the spinach wilt rather than fighting it; a real merge, because the pasta goes **into** the sauce; and `[source]` nutrition with the source's own contradiction named rather than silently resolved.

## Mode A fragment — long-lead and a supplied method

A different shape: work that happens hours earlier, cooking that happens before Clock 0, and a branch whose method the source never gave.

```text
Serves 4  ·  Per serve ~300 kcal  ·  Total ~1,200 kcal
P 13 g  ·  C 42 g  ·  F 9 g                     [est.]
Rajma only — rice adds ~205 kcal per serve.

Active ~27 min  ·  Total elapsed ~1 hr 15 + soaking

Rice method supplied by you, not the source:
wash · boil · low + cover 12 min

EARLIER
● SOAK RAJMA — 6 hr / overnight

BEFORE ACTIVE COOKING            (~45 min)
● PRESSURE-COOK RAJMA until soft
● CHOP ONION + GREEN CHILLI

TIMELINE — renders as the pasta example above.
```

The 45 minutes of pressure-cooking sits in total elapsed, never in active. The rice is a branch **only** because the method was supplied and credited — had the source said just "serve with rice," the correct output is `Rice — method not given` under **Serve with**, with no lane at all. And the rice would finish alongside the rajma on its own `✓` row: no merge, because rice and rajma share a plate rather than combining.

## Mode B — stir-fry

```text
Serves 2  ·  Per serve 410 kcal  ·  Total 820 kcal
P 34 g  ·  C 18 g  ·  F 22 g                 [source]

Legend  ● act  ◆ gate  ✓ done

MISE EN PLACE — in reach order, left to right
  1  Chicken, marinated      5  Peanuts
  2  Sauce, whisked          6  Scallion greens
  3  Chillies + peppercorns  7  Warm serving plate
  4  Garlic, ginger, whites

BEFORE HEAT
● WOK ON HIGH        ◆ until smoking (~3 min)

SEQUENCE — ~3 min, no pause once the oil goes in
0:00   ● Oil, swirl to coat
0:15   ● Chicken in, spread flat, leave still
0:45   ● Toss until just opaque
1:15   ● Remove to plate
1:20   ● Chillies + peppercorns        20 sec
1:40   ● Garlic, ginger, whites        30 sec
2:10   ● Chicken back + sauce, toss to gloss
2:40   ● Peanuts + greens
3:00   ✓ PLATE NOW — holds ~0
```

Figures are `[source]`, quoted as published and not recomputed. No interval markers and no prep tasks inside the sequence: there is no free time, and pretending otherwise is the failure Mode B exists to prevent.

## Mode C — set dessert

```text
Serves ~6 [est. from volume]  ·  Per serve ~340 kcal
Total ~2,040 kcal  ·  P 4 g  ·  C 26 g  ·  F 25 g
                                               [est.]
Active ~20 min  ·  Total elapsed ~4 hr 45

Legend  ● act  │ unattended  ┃ attended  ◆ gate

SERVE 20:00

SESSION 1 — start 15:15, about 15 min hands-on
T−4:45  🔪 Bloom gelatine in cold water
        │  5 min
T−4:40  ● Cream + sugar + vanilla, medium heat
        ┃  ◆ steaming, not simmering
T−4:33  ● Off heat · stir in gelatine until dissolved
T−4:31  ● Strain into moulds
T−4:30  ● Into the fridge

SET — unattended
        │
        │  4 hr minimum · good to 24 hr
        │
SESSION 2 — 19:55, about 5 min
T−0:05  ● Dip moulds in warm water 3 sec, unmould
T−0:02  🔪 Berries + mint
T−0:00  ✓ SERVE
```

No serving count in the source, so it is inferred from mould volume and marked. Sessions, not a continuous clock. The dish is `✓` only after the set, never at the end of Session 1.

---

# 13. Failure Modes

**Invented side method.** "Serve with rice" rendered as a rice branch with washing, boiling and a 12-minute cover. A branch needs a method from the source or the user — and a user-supplied one gets credited.

**Merge where there is none.** `└──→` drawn between a curry and its rice. They share a plate; they do not combine.

**Patching instead of re-running.** Adding a side by inserting a lane and leaving the old markers alone. Re-run from the source, carry forward what the user established, list what moved.

**Marker measuring the wrong thing.** `[+8–10 min]` at Clock 0 when a branch opens at 4 min. It counts to the next action in any lane.

**Marker floating away from its timestamp.** A `[+n]` two or three lines below the time it belongs to.

**`[+1 min]` markers.** Noise. Two timestamps a minute apart say it already.

**Simultaneous events stacked.** Two rows both labelled 23 min, one above the other, reading as a sequence.

**Double-booked cook.** A drain and an emulsion stir in the same minute in different lanes.

**Refusing a simple recipe.** Declining a timeline the user directly asked for because the recipe is short.

**Fabricated nutrition.** An estimate presented as the recipe's own figure, or calories quoted to the digit off an ingredient list.

**Calories cut for reduction.** Evaporation removes water, not energy.

**Frying oil counted in full.** The whole pan added when the food absorbed a tenth of it.

**Fabricated precision in time.** `[+8 min]` for "cook till the oil separates." Use `[≈8 min]` and let the cue carry the weight.

**Universal inference markers.** Every line `[≈]` when nothing was sourced. Use the header line.

**Lost tail.** Panna cotta `✓` at 12 minutes when it sets for four hours.

**Forced timeline on attended cooking.** A stir-fry as `0 min ●, 1 min ●, 2 min ●` is a numbered list in costume. Route to Mode B.

**Attention invisible.** Twenty minutes of frying drawn like twenty minutes of covered simmer.

**Forward-scheduling a dinner.** Four components from Clock 0, arriving at four different times.

**Just-in-time prep in a bake.** Weighing flour in a window that does not exist because the batter is mixed.

**Batches expanded.** Four fried loads as four event clusters.

**Rest treated as dead time.** Fifteen minutes of resting meat with the gravy unmade.

**Preheat elided.** Oven at temperature at Clock 0.

**Carryover ignored.** Pulling a roast at the finished temperature, then resting it past done.

**Range creep.** `~95–130 min` at the end of a roast.

**Unfenced plan.** A timeline in plain markdown, where a proportional font destroys the lanes.

---

# 14. Quality Check

Before returning, verify each. If any answer is no, revise.

## All modes

- **Fit** — right mode; and if the recipe is too simple, did I recommend rather than refuse?
- **Source** — do I actually have the method?
- **Provenance** — does every branch have a real method, and is a user-supplied one credited?
- **Revision** — if this replaces an earlier plan, did I re-run from the source, list what was carried forward, and say what moved?
- **Nutrition** — block at the top, per-serve and total, every figure tagged, rounded to the precision it deserves, source contradictions named?
- **Servings** — stated, or inferred and marked; totals only when neither is possible?
- **Exclusions** — uncosted accompaniments left out and named; unestimatable ingredients called out rather than guessed?
- **Fidelity** — quantities, temperatures, techniques, stated times unchanged?
- **Inference** — every supplied duration marked, or the all-inferred header used?
- **Gates** — readiness conditions, stop-conditions, setup preconditions and internal temperatures shown as gates rather than clock times?
- **Carryover** — pull temperature adjusted where it matters?
- **Preheat** — does everything reach temperature before it is needed?
- **Equipment** — capacity, sharing and contention resolved, or the conflict stated?
- **Tail** — after-block present, and the dish only `✓` once it is done?
- **Headline** — active and total elapsed both reported, not conflated?
- **Rendering** — fenced, near ~60 characters, every row surviving a wrap?
- **Load** — could one cook realistically execute this?

## Mode A only

- **Markers** — counting to the next action in **any** lane, sitting directly under their timestamps, skipping gaps of a minute or less?
- **Durations** — moved onto their events rather than into the marker column?
- **Collisions** — no two hands-on actions in the same minute in different lanes?
- **Rows** — simultaneous events sharing a row rather than stacking?
- **Clock 0** — the start of the continuous sequence?
- **Attention** — every rule at its correct weight, prep only in `│` or `╎` intervals?
- **Prep** — present and scheduled at the latest calm opportunity, baking excepted?
- **Branches** — genuinely separate, at most two lanes, starting where they should, merging only if they truly combine?
- **Batches** — cycles collapsed with recovery time and a holding instruction?
- **Convergence** — anchored to `T−n` if two or more components must arrive hot separately?

## Mode B only

- Mise en place complete and in reach order?
- Preheat gate in **Before heat** with its lead time, outside the sequence clock?
- Sequence free of interval markers and prep tasks?
- No-pause warning stated?

## Mode C only

- Sessions separated, each with its own hands-on estimate?
- Everything offset from serve rather than from zero?
- Minimum and maximum wait times both shown where the source gives them?
- Intermediate shelf lives flagged where those components are made?

---

# 15. Maintaining This Skill

*For whoever edits this file. Not part of a run.*

Rules and their demonstrations drift apart, and when they disagree the demonstration wins. Three times a worked example has contradicted a rule it was meant to illustrate — `[+1 min]` markers, marker placement, and a rice branch with no supplied method — and each time the example kept reproducing the bug after the rule had been fixed.

The defence is adjacency. Every rule worth demonstrating carries a short snippet directly beneath it. Those have never drifted, because you cannot edit the rule without seeing the snippet. Keep it that way:

- **When you change a rule, fix its snippet in the same edit.**
- **Keep exactly one full composite per mode in §12.** Do not add a second; two examples of one mode can contradict each other, and then neither is authoritative.
- **Prefer a fragment to a second full example** when a mode needs to show another shape. A fragment that omits markers and lanes cannot disagree with the full example about them — it only demonstrates what it uniquely owns.
- **After changing any rule, re-read §12 against it before shipping.**

Length is itself a risk: the longer this file gets, the less reliably any single rule is followed. Before adding a section, check whether an existing rule can absorb it.