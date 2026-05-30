+++
title = "Making Sense of the rEvo RMS on a Shearwater Petrel"
date = "2026-05-30"
author = "Bist"
authorTwitter = ""
cover = ""
tags = ["scuba", "rebreather", "rEvo", "CCR", "Shearwater", "RMS", "diving"]
keywords = ["rEvo RMS", "scrubber monitoring", "Shearwater Petrel", "RCT", "RST", "rebreather", "CCR scrubber", "temperature stick"]
description = "The rEvo Monitoring System confused me for far longer than it should have. Here's what RCT, RST and all those coloured symbols actually mean — and how I use them to plan dives."
showFullContent = false
readingTime = true
hideComments = false
+++

The rEvo RMS is one of those features I bought into without really understanding it. I knew it watched my scrubber, I knew green was good and red was bad, and I knew there were two numbers that went down during a dive. Beyond that? I was mostly nodding along and hoping.

That's a bad place to be with the thing that's keeping CO₂ out of your lungs. So I sat down with the manuals, worked through the maths, and figured it out properly. Here's the version I wish someone had handed me on day one.

## What the RMS actually does

RMS stands for **rEvo Monitoring System**. The important thing to get straight first: it does **not** measure CO₂. There's no CO₂ sensor in your loop.

What it does is measure *heat*. When your exhaled CO₂ reacts with the sorb, that reaction is exothermic — it warms up. Each scrubber has a temperature probe with fast-response sensors buried right in the absorbent, and the Petrel watches the warm spot move down the canister as the sorb gets used up. From the position and speed of that reaction front, it predicts how much usable scrubber you have left.

It's a prediction, not a measurement. Keep that in the back of your mind — it matters later.

## The two numbers: RCT and RST

Everything on the scrubber screen comes back to these two:

- **RCT — Remaining Cycle Time**: how long until you *must* swap (cycle) a canister. This is your planning number.
- **RST — Remaining Scrubber Time**: how long until *breakthrough* — CO₂ actually getting past the scrubber. This is your safety buffer, sitting underneath the RCT.

The rule I drilled into myself: **plan on RCT, and never plan a dive that lets it reach zero.** RST is the margin you fall back on, not the number you aim for. Diving into RST territory is an emergency, not a plan.

Because it's a live prediction tied to your current depth, temperature and workload, the RCT on the main screen **moves around**. Go deeper and it drops; come shallow and it climbs back up. That's not a glitch — it's the system recalculating for where you are right now. For most divers it ticks down by *less than* a minute per minute of diving, and that's exactly how it's supposed to behave. The algorithm deliberately assumes a high CO₂ output and a conservative breakthrough point, so it errs toward telling you to cycle early.

## Reading the screen without second-guessing yourself

This is where I used to get lost. The scrubber field starts with a **C** (for Cycle), followed by two symbols — **the first is the top scrubber** (on the exhale lung), **the second is the bottom one** (on the inhale lung).

One thing the official docs bury, and which cost me a confused morning: **rEvo's screenshots are from the older Predator, where "OK" shows as green. On the Petrel, that same OK state is white.** So everywhere the manual says "green," read "white" on your Petrel. Yellow/amber (warming or attention) and red (alarm) are the same on both.

Here's how I read the symbols now:

- **`?? amber`** — the system is asking the probes for data but hasn't got it yet. Don't breathe on the loop.
- **`?? white`** — talking to both probes, but no prediction yet (usually the scrubber is still cold). Fine to start pre-breathing.
- **`X red`** — that canister isn't communicating. Usually it's not seated properly against the board. Check it.
- **A number, e.g. `0:45`** — enough warm-up data to show a real RCT.

And the colon in `C:` is its own little status light. **Yellow colon = still warming up** (the number will keep rising as more of the canister comes up to temperature). **White colon = fully warm**, full canister length in use.

If you press through to the detail screen, each scrubber shows as **7 zones** that go grey → yellow → white as the reaction front warms them from the inlet down. All grey means nothing's happening yet; all white means fully warmed and you've hit your maximum prediction for that depth.

## The one new habit: pre-breathe to 0:45

This is the single most important operational change RMS introduces, and it's easy: **pre-breathe until the RCT shows at least 45 minutes and turns white.**

That tells you the scrubber is genuinely reacting and warming up. You do *not* have to wait for the whole canister to warm — that takes 20–40 minutes in the water and there's no need. Once it's active, above 0:45, and your logged RCT from the last dive covers your planned dive, finish your checks and go.

Two hard "don'ts" that I now treat as gospel:

- **Don't switch the Petrel off once the scrubber has started warming.** The algorithm needs a continuous record of temperature change over time. Interrupt it and some zones may never reach "ready," and the monitor may never come up properly for that session.
- **Don't breathe on the unit with the computer off**, for the same reason.

## Planning the next dive (the part with actual maths)

The bit that genuinely tripped me up. The RCT on the main screen is useless for planning the *next* dive — once a dive is over, that number means nothing. For planning you use the **Scrubber Times log**, which records the **minimum** RCT reached on each of the last dives, *and the depth where that minimum happened*.

Four rules of thumb:

1. Always read the **logged minimum RCT** and its recorded depth — never the main screen.
2. If that depth was **20 m or shallower, just call it 20 m** (scrubber duration barely changes between the surface and 20 m, and it keeps the sums clean).
3. If the **next dive is shallower** than (or equal to) the recorded depth, your available RCT is simply the logged minimum. No reduction.
4. If the **next dive is deeper**, RCT scales inversely with depth:

```
RCT(next) = RCT(previous, in minutes) × recorded depth ÷ planned depth
```

A worked example, because formulas always look scarier than they are. Say my logged minimum was **2h40 at 30 m**, and I want to dive to **40 m**:

```
2h40 = 160 minutes
160 × 30 ÷ 40 = 120 minutes = 2h00
```

So I've got two hours of cycle time for that 40 m dive. If the result had come out shorter than my planned dive, I'd cycle a canister first.

One more caveat with no tidy formula: **if the next dive will be more than 4 °C colder than the last one, just cycle.** And regardless of what the RMS logs for you — **write down your scrubber time after every dive anyway.**

## Cycling: the TOP-MARKER trick

When the plan says cycle, you only replace the near-saturated canister, not both. The habit that makes it foolproof:

**If you remove the TOP-MARKER, empty that canister. Immediately.**

So: pull the top canister (the one wearing the marker), unscrew the marker, and dump that sorb right away — now you can't possibly confuse it with the full one. Screw the marker onto the remaining full canister, put it back in the top position, refill the empty one and drop it in the bottom. Note which is which. Done.

## So is it magic? No.

It's decision support, not a CO₂ sensor. Temperature sticks infer breakthrough indirectly, and the probes do wear out — mine will fail eventually, and yours will too. The RMS makes me sharper and lets me stretch a scrubber safely further than the old "cycle every 2 hours" rule of thumb ever could. But it doesn't replace knowing my own CO₂ symptoms, keeping hard time limits in my head, and being ready to bail.

What it *did* do, once I actually understood it, was turn a screen full of cryptic symbols into something I can read at a glance. Which, when you're nine metres down watching a number tick toward zero, is worth the afternoon I spent figuring it out.

---

*If you want the condensed version, I also put together a printable one-page cheat sheet covering the symbols, the pre-breathe rule and the planning maths. Stick it in your save-a-dive kit.*
