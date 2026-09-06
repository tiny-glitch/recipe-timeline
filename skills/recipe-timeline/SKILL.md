---
name: recipe-timeline
description: "Convert a written recipe into an execution-order cooking timeline: when to act, how long until the next action, where prep fits, and how parallel components converge. Use when a recipe needs turning into a cooking plan."
---

# Recipe Timeline

Turn a conventional recipe into an execution plan for one cook.

A recipe lists actions in **written order**. This skill re-renders them in **execution order** — a cooking dependency graph laid out against a clock.

Each plan row answers three questions at once:

- **Where am I?** — cumulative position
- **How long until I must act?** — the interval marker
- **Am I free right now?** — the weight of the vertical rule

The underlying rule for prep:

> as late as practical, and as early as necessary

---

# 0. Route First: Mode and Fit

Before anything else, classify the recipe. Choosing the wrong mode is the most common failure.

**If the recipe has fewer than 5 actions and no meaningful timing or parallelism** — scrambled eggs, a salad, a smoothie — recommend a simple numbered sequence rather than a full plan, and say in one line why it would be overhead. **If the user explicitly asks for the timeline format, give it to them.** This threshold is a default, not a refusal.

Otherwise pick a mode:

## Mode A — Timeline (default)

At least one interval over 3 minutes and 6+ actions. Most stovetop mains, braises, stews, curries, pasta, sheet-pan dinners.

## Mode B — Sequence (attention-dominant)

**Most intervals under 90 seconds**: stir-fries, omelettes, tempering-heavy dishes, fast sautés — anything where the cook never leaves the pan.

There is no free time in Mode B, so do not pretend there is. Output:

1. **Mise en place, in reach order** — everything prepped and arranged before heat, listed in the order it will be grabbed.
2. **A before-heat block** carrying any preheat gate and its lead time, outside the sequence clock. The clock starts when the food hits the heat, not when the pan does.
3. **A fast sequence** with cumulative seconds and gates, no `[+n]` markers and no prep tasks inside it.
4. An explicit line: *"From heat on to plate is about N minutes with no pause. Everything above must be within arm's reach first."*

Mode B is the exception to "do not dump all mise en place before the clock." Here, dumping it there is correct.

## Mode C — Schedule (wait-dominant)

Active work is a small fraction of elapsed time: baking, bread, chilled and set desserts, fermentation, sous vide, overnight braises, anything with a multi-hour tail. Output **sessions anchored backward from serve time**, not a continuous clock from zero.

## Mixed

Many recipes are Mode A with a Mode C tail — banana bread is a 20-minute timeline plus 70 minutes of cooling. Render the timeline, then an **After active cooking** section. Do not mark the dish complete until the tail is done.

## Revising an existing plan

When the user changes a recipe that already has a plan — adds a side, drops a component, sets a serve time, doubles the quantity — **re-run the whole skill from the original source. Do not patch the previous output.**

A plan is a graph, not a list. One new component changes the merged action list, so markers downstream shift; it can also create a collision, consume the last free prep window, put a second pot on a burner, exceed the pot's capacity, or push the meal past two lanes and into serve-time anchoring. Local edits are exactly how a plan ends up internally inconsistent, with stale markers pointing at actions that have moved.

Re-running is cheap. What is not cheap is losing what the previous pass established, so restate it **in the reply, not the document** (§11) — stating it there makes it correctable rather than silently assumed:

- methods the user supplied that the source did not contain
- a serve time, serving count or container count they gave
- corrections they made to the last output
- anything they said they do differently

Where one of these is durable it also lands in the document, but as ordinary content, not as a record of the change: a supplied method as a branch with a one-line third-person provenance note (§11), a given serve time as the actual clock. What stays in the reply is that this pass is where it came from.

Then tell the user what moved — again in the reply, never as a document section — listing only what changed position or attention, not a recount:

```text
What moved  (reply only, never a section)
- Garlic 9 min → 11 min (pasta pot now starts at 4 min)
- Collision at 22 min: drain moved into the spinach wilt
- Two hot components now, so the plan anchors to serve
```

The user may already have the old plan in their head. Telling them what shifted is the point of re-running rather than patching — but it is a message to them, not a line in a document a stranger will cook from.

---

# 1. Source Gate

If ingredients are present but the **method** is missing, stop. Report that a faithful plan cannot be built. Do not invent a plausible recipe.

If the method is present but **times are missing** (very common on recipe blogs and in traditional recipes), proceed — but mark inferred durations per §7 and lead every cue-driven step with the cue, not the number.

---

# 2. Extract

From the source, capture: title, servings, ingredients, instructions, stated times, temperatures, equipment, doneness cues, accompaniments, and any stated nutrition figures.

Do not alter ingredient quantities.

If the user asks to scale the recipe, note that durations do not scale linearly — a doubled quantity browns slower, may need a larger pan, or may force batches (§7, repeat cycles).

---

# 3. Nutrition

Every output carries servings, energy and macros near the top, above the equipment and ingredients. The cook decides whether to make the dish from this block.

```text
## Nutrition *(calculated)*

- **Energy** ~300 kcal per serve · ~1,200 kcal total
- **Protein** 13 g · **Carbs** 42 g · **Fat** 9 g
```

Protein, carbohydrate and fat are the default three. Carry fibre, sugar or sodium only when the source gives them. Always show **both** per-serve and total.

## Provenance, always marked

`(from source)` — the recipe states the figures. Quote them verbatim. Do not recompute them, and do not "correct" them even if your own estimate disagrees.

`(calculated)` — computed by you from the ingredient list. Never present these as the recipe's own numbers.

**Omitted** — the ingredients cannot support an estimate. Leave the heading untagged and name the blocker in the list:

```text
## Nutrition

- **Energy** not estimated — "oil for deep frying" has no
  quantity, and absorbed oil dominates the figure.
```

The tag **goes in the section heading**, not on a line of its own beneath the list. A tag on its own line is an orphan: it belongs to neither the block above nor the one below, so a renderer gives it no anchor and it reads as a caption for whatever section follows. Any caveat that will not fit in the heading goes in a normal line under the list: *"rajma only; rice adds ~205 kcal per serve."*

Provenance is written in words, not brackets. Brackets earn their place on `[+n]` markers, which sit in a column of their own; in a heading there is no column, and brackets read as machine output.

The distinction mirrors `[+n]` / `[≈n]` for durations, and carries the same rule: an invented number presented as a sourced one is a fidelity failure.

## Servings

Per-serve figures need a serving count.

- Stated by the source → use it. A storage line like "portion into 6 containers" states it.
- Not stated → infer from total quantity and say so under the list: *"serving count calculated from volume."*
- Cannot be inferred → show totals only and say per-serve needs a serving count.

When the source gives per-serve figures only, multiplying out for the total is arithmetic on its numbers, not a new estimate. It stays `(from source)`.

## When the source contradicts itself

Raise it once, in the reply, and keep going. Do not wait for an answer, and do not render the reconciliation into the plan — the plan is what gets published, and a reader who only ever sees one set of figures has no use for the arithmetic behind the choice.

Resolve by taking the figures that match **the recipe as written**: the ingredient list governs, not a table calculated on some other quantity. Tag them `(from source)` like any other stated figures.

Beside the plan, once:

```text
The ingredient list says 20–30 g parmesan but the second
nutrition table is calculated on 66 g. I've used the
20–30 g figures — say if you want the other set.
```

If the user answers, re-run with their choice. If they don't, the plan already stands and nothing needs redoing.

## Estimating honestly

These are what make ingredient-list estimates wrong. Each has a rule:

- **Frying oil** — only the absorbed fraction counts, not the pan-full. Shallow and deep frying absorb roughly 10% of the oil by weight of food. If the recipe says "oil for frying" with no quantity, that defeats the estimate — omit rather than guess.
- **Discarded marinade** — count roughly a quarter, what clings to the food.
- **Brines and salted boiling water** — mostly discarded. Ignore unless the source tracks sodium.
- **"Salt to taste", "oil as needed"** — count as zero and say so.
- **Trim, bones, shells, peel** — estimate on edible yield, not purchase weight.
- **Reduction** — evaporation removes water, not energy. A reduced sauce has the same calories as before it reduced. Never scale the figure down for simmering.
- **Alcohol** — assume roughly half retained in a simmered dish, nearly all in an unheated one.
- **Accompaniments not costed in the recipe** — exclude them, and say the figure is for the dish alone.

## Precision discipline

An ingredient-list estimate is worth ±15% at best. Never render it more precisely than that supports: `~520 kcal`, not `518 kcal`. Round energy to the nearest 10 kcal and macros to the nearest gram. Do not put ranges on macros — round hard and let `(calculated)` carry the uncertainty. Ranges the **source** gives are preserved as written.

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

  ## Serve with
  Rice — method not given.

User supplies one — branch allowed, with its provenance noted:

  Rice — method not from the source
  wash · boil · low + cover 12 min

10 min   ├·······→ 🔪 Wash rice · pot on
```

A method may come from the source, from a second recipe, or from the user. When it comes from anywhere but the source, mark it — one short line in the third person beside the branch, naming what the source lacked rather than who supplied it or when (§11) — so the reader can see where those steps originated. Never manufacture one.

## Gates and stop-conditions

Conditions, not durations. Never proceed on the clock alone.

`◆` **proceed when true** — `◆ until oil shimmers`, `◆ oven at 200°C`, `◆ water at a rolling boil`, `◆ dough doubled`, `◆ skewer comes out clean`

`◇` **stop as soon as true** — `◇ just combined`, `◇ first sign of colour`, `◇ slightly underdone`. Opposite polarity to `◆`, and confusing the two ruins batters, custards and emulsions.

`🌡` **internal temperature** — use whenever the source gives one. A gate, never a sensory cue; it overrides the clock absolutely.

A gate also covers a **setup precondition** that makes a step irreversible if missed. Put it on the line above the action, not inside it:

```text
         │         ◆ cup by the sink
23 min   │         ● RESERVE 1 CUP · DRAIN
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

Put the preheat on the plan early enough that the gate is met when needed.

---

# 6. The Time Model

Every task lands in exactly one zone.

## EARLIER (long-lead)

A task belongs here when **it forces a wait you cannot fill with the rest of the prep** — soaking, proving, fermenting, thawing, curing, a long marinade. Judge behaviourally, not by a fixed threshold: a 20-minute marinade that runs while you prep everything else is pre-prep, not long-lead.

Do not draw an eight-hour timeline because something soaked overnight — collapse it to a block.

## BEFORE ACTIVE COOKING (Mode A) / BEFORE HEAT (Mode B)

Mode A: only what genuinely must be ready at Clock 0 — pre-cooked components used later in the main sequence, ingredients needed within the first minute, whole spices that hit hot oil immediately. Do not dump all mise en place here, except when the recipe is a bake (see the baking exception).

Mode B differs; see §0.

## TIMELINE / SEQUENCE / SESSIONS

Clock 0 is the start of the **main continuous execution sequence**, not the first thing ever done to an ingredient. Mode C has no Clock 0 — it uses offsets from serve (§9).

## AFTER ACTIVE COOKING (long-tail)

Resting, cooling, chilling, setting, maturing. Mandatory whenever it exists, and the zone most often dropped.

1. The dish is **not** `✓` until the tail is done. A cheesecake is not finished when the oven goes off.
2. A rest window is **prime working time**, not dead time. Meat resting 15 minutes is when the gravy gets made and the greens get cooked. Schedule into it deliberately.

## Headline numbers

Report both, and never conflate them. Work hidden in EARLIER or BEFORE ACTIVE COOKING belongs in the second number only:

`**Serves 4** · Active ~27 min · Total elapsed ~1 hr 15 + soaking`

Here 45 minutes of pressure-cooking sits outside the 27-minute clock. A rajma that hides it is not a 27-minute recipe.

---

# 7. Durations

## Marker syntax

- `[+5]` — **stated by the source**
- `[≈5]` — **inferred by you**
- `[+3–5]` — a stated range, preserved

Markers carry no unit. The legend says "minutes" once; repeating it on every row spends width on nothing.

Rendering an invented number in the same typography as a sourced one is a fidelity failure. Include the legend in every plan.

**When the source gives no durations at all**, universal `[≈]` marking distinguishes nothing and becomes noise. Drop the per-interval marker and put one line above the plan instead:

```text
Source gives no times — all intervals are estimates.
Cook to the cues, not the clock.
```

Then use plain `[+n]` throughout and let the cues carry the weight.

## What a marker measures — the gap to the next ACTION

A marker counts down to the next moment the cook must do something **anywhere in the recipe** — not to the next event in the same lane, and not to the end of whatever is currently cooking.

Compute markers against the **merged** action list of every lane. A branch opening at 4 min splits a ten-minute onion window into `[+4]` and `[+5]`; it does not stay `[+8–10]` because the onion is still going.

## Where a marker sits — immediately below its timestamp

Put the marker on the line **directly under the timestamp it follows**, so reading down the left column gives "here I am / here's how long I've got" with nothing in between. The cooking duration, the cue and any prep go on the lines after.

```text
 0 min   ● OIL + ONION, medium heat
 [+4]    │  8–10 min → soft and golden
         │
 4 min   ├·······→ ● PASTA POT ON
```

`8–10 min` is the doneness spec; `[+4]` is when the cook next has a job. Conflating the two is the most common marker error — invisible in a single-lane recipe, obvious the moment a branch interleaves.

## Threshold — no marker for a gap of 1 minute or less

Skip these entirely. Two consecutive timestamps a minute apart already say everything a `[+1]` would. Keep the action's own short duration inline instead:

```text
 9 min   ● ADD GARLIC
         ┃  1 min, don't let it colour
10 min   ● ADD PASSATA
```

The left column should carry only the gaps worth planning around.

## Ranges and compounding

Preserve source ranges on the **durations** — never collapse `3–5 min` to `4 min`.

But compute markers and cumulative timestamps along a single nominal path (the midpoint), or a long recipe accumulates into `~95–130 min`, which informs no one. Durations carry the uncertainty; positions and gaps stay single numbers. Prefix inferred positions with `~`.

## Repeat cycles (`↻`)

Batch work — frying in loads, pancakes, dosa, searing in two goes — is one event with a cycle, never N copies:

```text
14 min   ● FRY BATCH 1
 [≈20]   ╎  ↻ ×4 · ~4 min each + 1 min recovery
         ╎  ◆ hold cooked batches at 100°C
34 min   ● LAST BATCH OUT
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

Two lanes may hold events in the same minute. One cook cannot execute both. After placing every event, walk the merged plan and flag any two hands-on actions within a minute of each other in different lanes.

Resolve by moving the flexible one **into a genuinely passive window of the other**, not by nudging it arbitrarily:

- draining pasta while spinach wilts — fine; the wilt takes one stir and then looks after itself
- draining pasta while dairy is being emulsified into a sauce — a collision; move the drain earlier

If neither can move, say which one waits and what that costs.

## Hold tolerance — finished components

Annotate every completion with how long it will wait:

```text
✓ RICE — holds 20 min covered
✓ GREENS — holds ~3 min, do last
✓ ROAST — needs 15 min rest, do first
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
[+n]       stated gap to the next action, in minutes
[≈n]       inferred gap to the next action, in minutes
T−n        minutes before serve
├·······→  parallel component branch
└────────→  convergence (components that combine)
✓          component complete
→          sensory cue
```

## Width and fencing

**The plan always goes inside a fenced code block**, legend included, at the top. That fence is what guarantees monospace; outside one, a proportional font collapses the lanes and the layout becomes meaningless. Nothing else in the output is fenced — everything around the plan is markdown (§11).

**Target ~50 characters per line.** A phone in portrait renders roughly 30–40 before wrapping, and a wrapped row destroys the lane structure. Most of the width budget goes to the spine, so keep a branch lane close: open it around column 18, not column 28.

A budget, not a hard cutoff — a line slightly over is fine; a layout that works at exactly one width is not. The real rule: **never build a row whose meaning is lost when it wraps.** Keep each row independently readable and put the important content early in the line.

## Lanes and rows

No column headers — they collapse. A branch runs in a **fixed right-hand column** for the rest of its life, so anything sitting in that column is visibly the same component without needing a header. Open and close it with an arrow, and keep the lane's `│` running through the rows in between so the reader can follow it down the page.

**Simultaneous events share a row.** Never stack two events carrying the same timestamp on consecutive lines — that reads as a sequence, which is the opposite of what it means.

At most two lanes. If a third component needs one, give it its own block below the plan and cross-reference it by time.

---

# 11. Output Structure

The output is a **markdown document**. Only the plan is fenced (§10).

```text
# [Recipe name]

[One line saying what it is. Optional.]

**Serves X** · Active ~N min · Total elapsed ~N

## Nutrition *(from source)*        [or: *(calculated)*]

- **Energy** ~N kcal per serve · ~N kcal total
- **Protein** n g · **Carbs** n g · **Fat** n g
- [fibre, sugar or sodium only when the source gives them]

## Equipment

Anything beyond a knife, board and one pan. Flag pot capacity, and
any item or burner needed twice at once.

## Ingredients

One nested list, grouped by the moment each thing is used rather
than by component, each group labelled with its clock time. Groups
holding a single ingredient sit inline.

- **Before you start** — [item]
- **0 min · [role]**
  - [item]
  - [item]
- **N min** — [single item]

## Serve with

Accompaniments named in the source, without invented methods.

## Earlier

Long-lead only, when it exists.

## Before active cooking            [or: Before heat]

Only what genuinely must be ready.

---

## Timeline                         [or: Sequence, or: Schedule]

The legend, then the plan, in one fenced block.

---

## After active cooking

Rest, cool, chill, set. Mandatory when it exists.

## Storage and reheating

Only if requested or supported by the source.
```

The recipe name is `#`; every section heading is `##`.

**Sections that do not apply are omitted silently** — no placeholder, no "not applicable" line, in any real run. A worked example in §12 is the sole exception: it names what it skips, because an unexplained absence in an example is what teaches the next run to drop a section (§15).

**Emit no HTML.** `<details>`, `<div>` and inline styling do not survive common markdown previewers and publishing tools, which render the tags as literal text. If a destination needs collapsible sections, that is done at the destination.

## The deliverable is a file

Write the document to a markdown file named after the recipe in kebab-case — `batch-tomato-pasta.md` — and deliver that file. A plan is read in a kitchen, edited later, and published somewhere; all three want a file, and none of them want it trapped in a chat transcript.

The reply carries everything that is true of *this pass* rather than of the dish: a source contradiction (§3), what the user supplied or decided and when, what a revision moved, and why. None of it goes in the document. Do not paste the whole document into the reply as well — the file is the artefact.

A document is read by someone who was not here. Every line in it must still be true and useful to a cook who never saw the conversation, so it describes the dish as it now stands and never as it differs from a previous version. "Scaled from a 10-serve source", "your correction this pass", "the lemon moved to serving" — all of these describe an edit, not a recipe.

Provenance is the exception, and only where it changes how the recipe is read: a method or a storage note that came from somewhere other than the source is marked in one short line beside the content it qualifies, in the third person. Not a section, and never a log.

## Spacing belongs to the destination

Blank lines in markdown do not scale. The renderer's stylesheet decides every vertical gap, so a document cannot be made to breathe by padding the source — two blank lines and one produce identical output. What you control is structure, and three things do the work:

- **Heading level.** Sections are `##`, never `###`. Renderers style h3 tight because it is meant to be subordinate to something; these are top-level sections and want h2's margins. This is the single biggest lever.
- **Horizontal rules.** `---` is the only primitive that forces a visible break in every renderer. One above and one below the plan, the block that most needs isolating.
- **No orphan lines.** A line belonging to neither the block above nor the one below has no anchor and reads as a caption for whatever follows. This is why provenance goes in the heading.

If a destination still renders tight after that, it is a stylesheet problem there. Do not answer it by restructuring the output.

Ingredients grouped by moment is the point of that section, not a formatting preference: it is what lets "1 cup reserved pasta water" appear in the 24 min group annotated with where it came from, a relationship a conventional ingredient list cannot express.

---

# 12. Worked Examples

These are integration tests, not the teaching material — the rules are taught by the snippets beside them. Each shows one mode end to end.

## Mode A — tomato pasta with beans, peas and spinach

Delivered as `batch-tomato-pasta.md`.

# Batch Tomato Pasta

Beans, peas, spinach and cottage cheese. Six containers, Sunday to Friday.

**Serves 6** · Active ~28 min · Total elapsed ~32 min

## Nutrition *(from source)*

- **Energy** ~540–590 kcal per serve · ~3,240–3,540 kcal total
- **Protein** 24–27 g · **Carbs** 75–82 g · **Fat** 12–15 g
- **Fibre** 13–16 g · **Saturated fat** 3–4.5 g

## Equipment

- Your deepest pan or pot — finished volume is around 4 litres, larger
  than the wording implies.
- A second pot for the pasta. Both burners run from minute 4.

## Ingredients

Grouped by when they're used.

- **Before you start** — 1 large brown onion, finely diced
- **0 min · base**
  - 1 tbsp olive oil
  - The diced onion
  - A pinch of salt
- **4 and 14 min · pasta pot**
  - 500 g pasta
  - Salt for the water
- **9 min** — 5 garlic cloves, minced
- **10 min · sauce**
  - 700 g passata or crushed tomatoes
  - 1–2 tsp Italian herbs
  - ½ tsp chilli flakes (optional)
  - Generous black pepper
- **19 min · seasoning**
  - 1–2 tsp balsamic vinegar
  - Salt, gradually, to taste
- **20–22 min · add-ins**
  - 2 cans cannellini beans, drained
  - 450–500 g frozen peas
  - 1 bag baby spinach (~120–150 g)
- **24 min · creaminess**
  - 200 g cottage cheese
  - 20–30 g parmesan, finely grated
  - 1 cup reserved pasta water (taken at 23 min)

## Before active cooking

- **Dice the onion** (~3 min)

---

## Timeline

```text
Legend
  ●  act    │  free    ┃  stay at the pan
  ◆  go when true    ◇  stop when true
  🔪 prep, into a free window    ✓  done
  [+n]  minutes to your next action, either lane
  Right-hand lane is the pasta.

 0 min   ● OIL + ONION + PINCH SALT, medium
 [+4]    │  8–10 min → soft, golden, savoury
         │
         │  🔪 Mince garlic
         │
 4 min   ├·······→ ● PASTA POT ON, salt well
 [+5]    │         │  ◆ to a rolling boil
         │         │
         │  🔪 Measure herbs, chilli, pepper
         │         │
 9 min   ● GARLIC  │
         ┃  1 min, don't let it colour
10 min   ● PASSATA + HERBS + CHILLI + PEPPER
 [+4]    │  simmer 8–10 min
         │         │
         │  🔪 Drain + rinse cannellini
         │         │
14 min   │         ● PASTA IN
 [+5]    │         │  ~9 min ◇ slightly underdone
         │         │
         │  🔪 Grate parmesan
         │  🔪 Peas, spinach, cottage cheese out
         │         │
19 min   ● BALSAMIC · TASTE · SALT GRADUALLY
         │  → until savoury and rounded
20 min   ● BEANS + FROZEN PEAS
 [+2]    │  simmer 2–3 min
         │         │
22 min   ● SPINACH · stir once, let it wilt
         │         ◆ cup by the sink
23 min   │         ● RESERVE 1 CUP · DRAIN
         │         ✓ PASTA — holds ~3 min
24 min   ● LOW HEAT · COTTAGE CHEESE + PARMESAN
 [+2]    ┃  + splash pasta water · DO NOT BOIL
26 min   └────────→ ● ADD PASTA · MIX
 [+2]    ┃  adjust salt, pepper, pasta water
28 min   ✓ DONE
```

---

## Storage and reheating

- Portion into 6 containers. Keeps Sunday to Friday in the fridge.
- Reheat with a small splash of water or milk.

Not applicable to this recipe: **Serve with** (the source names no accompaniment), **Earlier** (no soak, marinade or thaw), **After active cooking** (nothing rests, cools or sets). A real plan omits these silently; a worked example names them (§15).

This exercises: markers computed across both lanes and sitting under their timestamps; no `[+1]`; cooking durations riding on their events; a setup gate before an irreversible step; a `◇` stop-condition; a resolved collision — the drain sits inside the spinach wilt rather than fighting it; a real merge, because the pasta goes **into** the sauce; ingredients grouped by the moment they are used, which is what puts the reserved cup of pasta water in the 24 min group annotated with where it came from; and the markdown document of §11 — `##` sections, provenance in the heading, rules isolating the plan — wrapping a single fenced block.

## Mode A fragment — long-lead and a supplied method

A different shape: work that happens hours earlier, cooking that happens before Clock 0, and a branch whose method the source never gave.

# Rajma with Rice

**Serves 4** · Active ~27 min · Total elapsed ~1 hr 15 + soaking

## Nutrition *(calculated)*

- **Energy** ~300 kcal per serve · ~1,200 kcal total
- **Protein** 13 g · **Carbs** 42 g · **Fat** 9 g

Rajma only; rice adds ~205 kcal per serve.

## Earlier

- **Soak the rajma** — 6 hr or overnight

## Before active cooking

- **Pressure-cook the rajma** until completely soft (~45 min)
- **Chop** the onion and green chilli

---

## Timeline

Renders as the pasta example above.

The 45 minutes of pressure-cooking sits in total elapsed, never in active. The rice is a branch **only** because a method was supplied, and it carries its provenance as one line beside the branch — `Rice — method not from the source` — in the third person, so the reader learns which part of the recipe the source never contained without a receipt of who asked or when (§11). Had the source said just "serve with rice" with no method, the correct output is `Rice — method not given` under **Serve with**, with no lane at all. And the rice would finish alongside the rajma on its own `✓` row: no merge, because rice and rajma share a plate rather than combining.

## Mode B — stir-fry

# Kung Pao Chicken

**Serves 2** · Active ~3 min · Total elapsed ~6 min

## Nutrition *(from source)*

- **Energy** 410 kcal per serve · 820 kcal total
- **Protein** 34 g · **Carbs** 18 g · **Fat** 22 g

## Before heat

In reach order, left to right. Everything here is within arm's reach before the wok goes on.

1. Chicken, marinated
2. Sauce, whisked
3. Chillies + Sichuan peppercorns
4. Garlic, ginger, scallion whites
5. Peanuts
6. Scallion greens
7. Warm serving plate

- **Wok on high** — ◆ until smoking (~3 min)

---

## Sequence

```text
Legend
  ●  act    ◆  go when true    ✓  done

0:00   ● Oil, swirl to coat
0:15   ● Chicken in, spread flat, leave still
0:45   ● Toss until just opaque
1:15   ● Remove to plate
1:20   ● Chillies + peppercorns      20 sec
1:40   ● Garlic, ginger, whites      30 sec
2:10   ● Chicken back + sauce, to gloss
2:40   ● Peanuts + greens
3:00   ✓ PLATE NOW — holds ~0
```

---

From heat on to plate is about 3 minutes with no pause.

This example demonstrates the Mode B sequence only; the surrounding document is §11's, unchanged. No interval markers and no prep tasks inside the sequence: there is no free time, and pretending otherwise is the failure Mode B exists to prevent.

## Mode C — set dessert

# Vanilla Panna Cotta

**Serves ~6** · Active ~20 min · Total elapsed ~4 hr 45

## Nutrition *(calculated)*

- **Energy** ~340 kcal per serve · ~2,040 kcal total
- **Protein** 4 g · **Carbs** 26 g · **Fat** 25 g

Serving count calculated from mould volume.

---

## Schedule

Serve at 20:00.

```text
Legend
  ●  act   │  unattended   ┃  stay at the pan
  ◆  go when true   🔪 prep   ✓  done

SESSION 1 — 15:15, about 15 min hands-on
T−4:45  🔪 Bloom gelatine in cold water
        │  5 min
T−4:40  ● Cream + sugar + vanilla, medium
        ┃  ◆ steaming, not simmering
T−4:33  ● Off heat · stir in gelatine
T−4:31  ● Strain into moulds
T−4:30  ● Into the fridge

SESSION 2 — 19:55, about 5 min
T−0:05  ● Dip moulds in warm water 3 sec
T−0:02  🔪 Berries + mint
T−0:00  ✓ SERVE
```

---

## After active cooking

- **The set** — 4 hr minimum in the fridge, good to 24 hr. The dish is
  not done until this is.

This example demonstrates the Mode C schedule only; the surrounding document is §11's, unchanged. The set is an **After active cooking** section, not a gap in the schedule — the dish is `✓` only after it, never at the end of Session 1.

---

# 13. Failure Modes

Each of these has happened. They are here because the symptom is not obvious from the rule alone — a rule whose violation looks exactly like the rule stated backwards is covered by §14 instead.

**Missing a required section.** A plan with no Ingredients list, because the worked example it anchored on did not have one. Every applicable §11 section appears.

**Plan left in the reply.** No file written, so there is nothing to carry to the kitchen or publish. The file is the artefact.

**Sections at `###`.** h3 is styled tight in nearly every renderer and the document reads cramped. Sections are `##`.

**Orphan provenance line.** `*(from source)*` stranded between the nutrition list and the next heading, where it reads as a caption for the wrong section. It goes in the Nutrition heading.

**Padding the source for spacing.** Extra blank lines to make a document breathe. Markdown collapses them; the levers are heading level, rules, and the destination's stylesheet.

**Invented side method.** "Serve with rice" rendered as a rice branch with washing, boiling and a 12-minute cover. A branch needs a method from the source or the user — and a user-supplied one gets credited.

**Merge where there is none.** `└──→` drawn between a curry and its rice. They share a plate; they do not combine.

**Blocking on a contradiction.** Stopping to ask which nutrition table is right, or rendering the reconciliation into the plan. Resolve to the recipe as written, flag it once beside the plan, keep going.

**Session leaked into the document.** A published recipe carrying "scaled from the original", "carried forward" or "what moved" — true of the edit, meaningless to the cook. The re-run rule (§0) exists so the document does not accumulate its own history; writing that history into a section puts it back.

**Marker measuring the wrong thing.** `[+8–10]` at Clock 0 when a branch opens at 4 min. It counts to the next action in any lane, so it splits into `[+4]` and `[+5]`.

**Units on every marker.** `[+4 min]` twelve times over. The legend says minutes once.

**Simultaneous events stacked.** Two rows both labelled 23 min, one above the other, reading as a sequence when they are concurrent.

**Double-booked cook.** A drain and an emulsion stir in the same minute in different lanes. One pair of hands.

**Calories cut for reduction.** Lowering the figure because a sauce simmered down. Evaporation removes water, not energy.

**Frying oil counted in full.** The whole pan added when the food absorbed a tenth of it.

**Fabricated precision in time.** `[+8]` for "cook till the oil separates." Use `[≈8]` and let the cue carry the weight.

**Lost tail.** Panna cotta `✓` at 12 minutes when it sets for four hours.

**Forced timeline on attended cooking.** A stir-fry as `0 min ●, 1 min ●, 2 min ●` is a numbered list in costume. Route to Mode B.

**Forward-scheduling a dinner.** Four components started from Clock 0, arriving at four different times. Anchor to `T−n`.

**Just-in-time prep in a bake.** Weighing flour in a window that does not exist because the batter is already mixed.

**Batches expanded.** Four fried loads as four event clusters instead of one `↻` cycle.

**Rest treated as dead time.** Fifteen minutes of resting meat scheduled with nothing, while the gravy sits unmade.

**Carryover ignored.** Pulling a roast at the finished temperature, then resting it past done.

**Range creep.** `~95–130 min` at the end of a roast. Nominal path for positions and gaps.

---

# 14. Quality Check

Before returning, verify each. If any answer is no, revise.

## All modes

- **Fit** — right mode; and if the recipe is too simple, did I recommend rather than refuse?
- **Deliverable** — written to `<recipe-slug>.md` and delivered as a file, with the reply carrying only what does not belong in the document?
- **Structure** — a markdown document per §11, every applicable section present, Ingredients among them, the plan and only the plan fenced, no HTML anywhere?
- **Typography** — recipe name `#`, sections `##`, provenance in the Nutrition heading, a rule above and below the plan, no orphan lines?
- **Source** — do I actually have the method?
- **Provenance** — does every branch have a real method, and is a user-supplied one credited?
- **Revision** — if this replaces an earlier plan, did I re-run from the source, and put what moved in the reply rather than the document?
- **Durability** — does every line still read correctly to someone who never saw this conversation? No section, note or aside defined by what changed, who asked, or which pass this is.
- **Nutrition** — per-serve and total, tagged `(from source)` or `(calculated)`, rounded to the precision it deserves?
- **Servings** — stated, or calculated and said so; totals only when neither is possible?
- **Contradictions** — resolved to the recipe as written, flagged once beside the plan, kept out of the plan itself?
- **Exclusions** — uncosted accompaniments left out and named; unestimatable ingredients called out rather than guessed?
- **Fidelity** — quantities, temperatures, techniques, stated times unchanged?
- **Inference** — every supplied duration marked, or the all-inferred header used?
- **Gates** — readiness conditions, stop-conditions, setup preconditions and internal temperatures shown as gates rather than clock times?
- **Carryover** — pull temperature adjusted where it matters?
- **Preheat** — does everything reach temperature before it is needed?
- **Equipment** — capacity, sharing and contention resolved, or the conflict stated?
- **Tail** — after-block present, and the dish only `✓` once it is done?
- **Headline** — active and total elapsed both reported, not conflated?
- **Rendering** — legend inside the fence, near ~50 characters, every row surviving a wrap?
- **Load** — could one cook realistically execute this?

## Mode A only

- **Markers** — counting to the next action in **any** lane, sitting directly under their timestamps, skipping gaps of a minute or less, carrying no unit?
- **Durations** — moved onto their events rather than into the marker column?
- **Collisions** — no two hands-on actions in the same minute in different lanes?
- **Rows** — simultaneous events sharing a row rather than stacking?
- **Clock 0** — the start of the continuous sequence?
- **Attention** — every rule at its correct weight, prep only in `│` or `╎` intervals?
- **Prep** — present and scheduled at the latest calm opportunity, baking excepted?
- **Branches** — genuinely separate, at most two lanes, opening near column 18, merging only if they truly combine?
- **Batches** — cycles collapsed with recovery time and a holding instruction?
- **Convergence** — anchored to `T−n` if two or more components must arrive hot separately?

## Mode B only

- Mise en place complete and in reach order?
- Preheat gate in **Before heat** with its lead time, outside the sequence clock?
- Sequence free of interval markers and prep tasks?
- No-pause line stated?

## Mode C only

- Sessions separated, each with its own hands-on estimate?
- Everything offset from serve rather than from zero?
- The wait in **After active cooking**, with minimum and maximum where the source gives them?
- Intermediate shelf lives flagged where those components are made?

---

# 15. Maintaining This Skill

*For whoever edits this file. Not part of a run.*

Rules and their demonstrations drift apart, and when they disagree the demonstration wins. Three times a worked example contradicted a rule it was meant to illustrate — `[+1]` markers, marker placement, and a rice branch with no supplied method — and each time the example kept reproducing the bug after the rule had been fixed.

A fourth instance was drift by **omission**: §11 required Ingredients, Equipment and Serve with; the Mode A example contained none of them; a fresh run reproduced the gap. Omission is harder to catch than contradiction, because a missing section presents no line to compare against the rule and nothing looks wrong on inspection.

The defence is adjacency. Every rule worth demonstrating carries a short snippet directly beneath it. Those have never drifted, because you cannot edit the rule without seeing the snippet. Keep it that way:

- **When you change a rule, fix its snippet in the same edit.**
- **Keep exactly one full composite per mode in §12.** Two examples of one mode can contradict each other, and then neither is authoritative.
- **Prefer a fragment to a second full example** when a mode needs to show another shape. A fragment that omits markers and lanes cannot disagree with the full example about them.
- **The Mode A composite must exercise every applicable §11 section, and name the ones it skips.** Naming a skipped section turns an absence into a visible decision — the only defence against drift by omission, since adjacency cannot help when there is no snippet to sit beside.
- **The Mode B and C examples are exempt, and say so in one line.** They demonstrate their own plan shape only.
- **A change to document typography touches all four examples.** Heading level, provenance placement and rules render in every one, so a §11 edit that stops at §11 leaves four counter-examples behind.
- **State each rule once.** §13 carries only failures whose symptom is not obvious from the rule; anything that is just the rule restated belongs in §14 alone.
- **After changing any rule, re-read §12 against it before shipping.**

Length is itself a risk: the longer this file gets, the less reliably any single rule is followed. Before adding a section, check whether an existing rule can absorb it.
