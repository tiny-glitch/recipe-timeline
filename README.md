# Recipe Timeline

A Claude skill that turns a written recipe into an **execution-order cooking timeline** — when to act, how long until the next action, where prep actually fits, and how parallel components converge.

Recipes are written in the order someone wrote them down. That is rarely the order you should cook in.

```
1. Chop onion.          →     0 min   ● HEAT OIL
2. Chop capsicum.                     │  🔪 Chop onion
3. Heat oil.                  2 min   ● ADD ONION
4. Cook onion 5 min.          [+5]    │  🔪 Chop capsicum
5. Add capsicum.              7 min   ● ADD CAPSICUM
```

The skill rebuilds the recipe as a dependency graph, then renders it against a clock. Prep gets scheduled **as late as practical, and as early as necessary** — into the gaps that genuinely exist, rather than all piled up before you start.

## What it looks like

A weeknight pasta, six portions, one cook:

```
Serves 6  ·  Per serve ~540–590 kcal
Total ~3,240–3,540 kcal                      [source]
P 24–27 g · C 75–82 g · F 12–15 g

Active ~28 min  ·  Total elapsed ~32 min

Legend  ● act  │ free  ┃ attended  ◆ gate  ◇ stop
        [+n] to next action · 🔪 prep · ✓ done
        Right-hand lane is the pasta.

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
            │               │
19 min      ● BALSAMIC · TASTE · SALT GRADUALLY
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

The recipe as written says *cook the pasta first*. Pasta holds for about zero minutes and the sauce takes 26, so the timeline starts the water at minute 4 instead and the pasta drains three minutes before it's needed.

## Reading it

| | |
|---|---|
| `[+4 min]` | how long until you next have to do something — **in any lane**, not just this one |
| `│` `┃` | free versus hands-on. `┃` means stay at the pan |
| `◆` | a gate: don't proceed until it's true (*rolling boil*, *oven at 200°C*) |
| `◇` | a stop-condition: stop the moment it's true (*just combined*, *slightly underdone*) |
| `🔪` | prep, placed in a window that genuinely fits it |
| `├···→` `└───→` | a parallel component, and a merge where two things actually combine |
| `[source]` `[est.]` | nutrition quoted from the recipe, versus computed from the ingredients |

## Install

**As a plugin** (recommended — updates when this repo does):

```
/plugin marketplace add tiny-glitch/recipe-timeline
/plugin install recipe-timeline@tiny-glitch
```

**Manually**, for one machine:

```bash
git clone https://github.com/tiny-glitch/recipe-timeline
cp -r recipe-timeline/skills/recipe-timeline ~/.claude/skills/
```

**In a single project**, so it loads for anyone who clones it:

```bash
cp -r recipe-timeline/skills/recipe-timeline .claude/skills/
```

Then paste a recipe and ask for a timeline.

## How it decides

Not every recipe wants the same shape, so the skill routes first:

- **Timeline** — the default. Stovetop mains, braises, curries, pasta, sheet-pan dinners.
- **Sequence** — when nearly every interval is under 90 seconds. Stir-fries, omelettes, tempering. There is no free time, so it gives you mise en place in reach-order and a fast run instead of pretending you can chop mid-wok.
- **Schedule** — when the waiting dominates. Baking, bread, set desserts, ferments. Anchored backward from when you want to eat, in sessions rather than one clock.

It also refuses to invent things. A recipe that says *serve with rice* and gives no rice method gets a "serve with" line, not a fabricated rice timeline. Durations the source stated are marked differently from durations the skill estimated. And where a recipe gives its own nutrition figures, those are quoted rather than recomputed.

## Design notes

Two ideas do most of the work.

**Attention is rendered, not just calculated.** A twenty-minute simmer and twenty minutes of frying are both twenty minutes, and only one of them is free. The weight of the vertical rule says which.

**The clock answers "when do I act," not "how long does this cook."** Those are the same number only until a second component interleaves, at which point conflating them quietly corrupts every timestamp downstream. Cooking durations ride with their events; the left column is strictly time-to-next-action.

`SKILL.md` also carries a maintenance section, because the recurring failure while building this wasn't wrong rules — it was worked examples silently drifting out of sync with rules that had been fixed. Examples outweigh prose, so a stale example keeps reproducing a bug after the rule is right.

## License

MIT — see [LICENSE](LICENSE). Fork it and retune it for your own kitchen.
