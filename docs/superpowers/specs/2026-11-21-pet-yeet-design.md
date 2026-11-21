# Pet Yeet — Design

**Date:** 2026-11-21
**Status:** approved by Robin, 2026-11-21
**Platform:** Roblox

---

## Intent and why

Robin wants to build Roblox games for the feeling of watching real people play
something he made, and to find out honestly whether earning money from Roblox is
actually possible.

That second motive is framed as a **test**, not a bet. So the first game is
deliberately a cheap, fast experiment to learn from — not a masterpiece. The
realistic day-one result is a few hundred players and near-zero revenue. The real
prize is what we learn and the proof that the whole pipeline works end to end:
build, publish, measure, decide.

## The game

**One verb: you throw your pet as far as you can.**

The loop:

1. A power meter sweeps across the screen. Press to release the throw.
2. Release timing applies a multiplier. A perfect release triggers a bigger
   **"PERFECT!"** throw animation.
3. The pet flies along a calculated arc. The camera follows it.
4. Distance converts to coins, counting up during the flight.
5. Coins buy throw-power upgrades and new pets, which make the next throw go further.
6. A global leaderboard tracks the furthest single throw.

The craft lives entirely in the **animation and feel**: the wind-up, the release,
the pet's flight and tumble, the impact, the number climbing. The mechanic is a
one-liner on purpose.

## Goals

- A published, playable game with the full loop working end to end
- Fun with exactly **one** player in the server
- Works properly on phones
- Produces at least one genuinely **clippable** moment (the perfect throw, the absurd number)
- Monetisation wired in, so the money question can actually be tested
- Built with the kits Robin already owns

## Non-goals (version one)

- Rebirths, trading, pet fusion, egg hatching
- Any interaction between players
- More than one map
- Real physics simulation
- *Don't Drop The Egg* — that is the next project, not part of this one

## Anti-goals

Things that would make this a failure even if it technically works:

- A game that is boring with one player in the server
- A game that needs a populated server to be fun
- A throw that feels weak or cheap — **the feel is the product**
- Distances the player cannot learn to influence
- Shipping so late that the simple-verb meta has moved on
- A build that only works on Robin's machine by accident and cannot be rebuilt

## Constraints

- **Roblox Studio and Blender run on Robin's Mac only.** Cloud sessions cannot reach
  them unless a folder is shared via `Settings → Claude Code → Remote Control`.
- Building uses the fwjelaa kits on the Mac Desktop: `Roblox-Animations-by-fwjelaa`
  (AnimKit), `Roblox-Maps-by-fwjelaa` (MapKit),
  `Picture-to-Roblox-Model-by-fwjelaa` (cuhkit), and `UI Icon Pack`.
- **Robin directs; he does not do fiddly technical work.** No "place this part" or
  "tweak this number" requests.
- Must work on phones — most Roblox players are mobile.
- Publishing, the store page and thumbnails stay with Robin.
- No kit covers gameplay systems (saving, leaderboards, purchases) or game feel
  beyond character animation (particles, screen shake, sound). That Luau gets
  written from scratch.

## Approaches considered

| | Approach | Verdict |
|---|---|---|
| **A** | **Real physics throw.** Pet is a true physics object; it bounces and rolls, distance is emergent. | **Rejected.** Network-inconsistent across clients, painful to tune, cheat-prone, worst on phones. |
| **B** | **Scripted arc with upgrade multipliers.** Fixed animation, then a calculated flight path from the player's stats. The proven simulator formula. | Solid but **no skill** — press and watch. |
| **C** | **Timing meter plus scripted arc.** One button and a power meter; timing gives a multiplier and a perfect release gets its own bigger animation. Then the calculated arc. | **Chosen by Robin.** |

**Why C:** it keeps B's predictable economics and phone-friendliness — which is what
makes the money question testable at all — while adding one moment of real skill and
a clippable perfect-hit animation, which is the part Robin cares about. Barely more
work than B, and it avoids every one of A's networking problems.

## The how, as decided

- **No real physics.** The pet's flight is a calculated arc.
- `distance = base × throw-power stat × pet multiplier × timing multiplier`
- One button, one power meter.
- A perfect release gets its own larger animation plus a visual and audio flash.
- The camera follows the pet in flight.
- Global leaderboard: furthest single throw.
- One map.
- Three or four pets, each with a distinct multiplier and flight character.
- A handful of throw-power upgrade levels.

## Left to the builder

These are **my calls**, not Robin's decisions — flagged so he can overrule any of them:

- **Distance formula numbers and the upgrade curve** — tuned by feel in Studio,
  starting conservative.
- **Perfect-timing window width** — generous at first. Punishing meters lose casual players.
- **When coins are awarded** — counted up during the flight rather than on landing; it reads better.
- **Which pets** — chosen from what cuhkit produces well and what the icon pack supports.
- **Sound design** — no kit covers it; written from scratch.
- **Saving** — Roblox DataStore, holding best distance, coins, upgrade levels and owned pets.
- **Monetisation** — two gamepasses: **throw power** and **double coins**. A "luck"
  gamepass was considered and **dropped**: with hatching, fusion and rebirths all
  non-goals, there is no randomness in v1 for luck to act on. Adding a random
  pet-roll system to justify it would cross into the non-goals.
- **Other players' throws** — each flight renders client-side for the thrower only.
  Simpler, cheaper, and nothing in v1 depends on seeing someone else's pet fly.
- **Leaderboard** — OrderedDataStore behind an in-world board.
- **Distance representation** — a hand-built opening stretch, then procedurally
  tiled segments for the long distances.
- **Mobile input** — tap anywhere.
- **Clips** — v1 ships with no replay or cinematic camera. Robin screen-records.
  A no-HUD replay, slow-motion perfect-release beat and shareable personal-best
  card are all real features with real cost; they are candidates for v2 once we
  know the game is worth clipping.

## How "feel" gets tuned

"The feel is the product" and Claude cannot perceive feel. So:

**Robin playtests and describes it in plain words** — "the wind-up is too slow",
"the meter is too fast", "the impact needs more weight". That is **directing, not
fiddly technical work**, and it is the single most valuable thing he does on this
project.

To make that work, Claude builds a **live tuning config** — one module holding
meter speed, wind-up duration, arc height, hang time, screen shake, flash
intensity and impact timing, all adjustable in one place. Every sentence Robin
says maps to exactly one number. No hunting through code, and no asking him for
numbers.

## Robin's tasks (nobody else can do these)

- Mint the two gamepasses and hand over their IDs — **early**, so the purchase
  path gets tested rather than stubbed
- Publish the game and own the store page and thumbnails
- Record and post the launch clips
- Playtest and describe how it feels

## Decisions on the review's questions

A design review on 2026-11-21 raised four questions the plan needed. Robin
delegated all four ("I let you pick") and approved Rojo explicitly.

1. **Time budget — about one week.** Publish rough but live, then improve in
   public. Consequences: one hand-built lane, pets kept simple, two upgrade
   tiers at launch, no bespoke sound design in v1. This follows directly from
   the anti-goal about the simple-verb meta moving on.
2. **The map — a bright cartoon launch hill.** A grassy slope you throw out
   over, with the distance stretching away into the sky. Hand-built opening
   stretch, procedurally tiled beyond it. Robin is the director on taste and
   can overrule this at any point; it is a Claude call made in his absence.
3. **The money test — one real purchase by a stranger counts as "yes".**
   Small, achievable, and it genuinely answers the question Robin asked.
   **This has a build consequence:** funnel analytics must be wired in
   *before* publish — join → first throw → first upgrade → first purchase —
   not bolted on afterwards.
4. **Rojo — approved.** The game's source lives as files in this repo, not
   trapped inside a binary place file. This resolves the rebuildability
   anti-goal, and lets Claude work when Studio is closed. One-time ask of
   Robin: click to install the Studio plugin.
   **Custom audio — still unknown.** Robin did not confirm whether his account
   is age-verified, and Roblox restricts custom audio uploads. Default: design
   around **Roblox's free audio library** from the start. If Robin later
   confirms verification, custom sound becomes a v2 upgrade. Never block on this.

## Facts worth keeping (verified 2026-11-21)

- **DevEx:** $0.0038 per earned Robux; $0.0054 for verified US 18+ players;
  **30,000 Robux minimum** to cash out (about $114).
- The **median** DevEx participant earns roughly **$1,500 a year**. The top ten
  creators took **$657 million** between them. Money is real and savagely unequal.
- **Current meta is simple-verb games.** *Steal An Egg* is #1 (around 820,000 average
  concurrent in November, 7.6M peak). *Throw Food For Animals* is in the top five.
- *Grow a Garden*'s first version was reportedly built by a 16-year-old in about **three days**.
- Virality on Roblox is **clip-driven**: one viral short earns algorithm placement,
  which earns players, which generates more clips.
- "Egg" is at peak saturation. The durable pattern is the *shape* — one verb,
  collection, clips — not eggs specifically.

## How we work together

- Robin decides; Claude builds. Robin is the director.
- Claude never asks Robin to do fiddly technical work.
- Claude tells the truth about money and odds, including unwelcome truths.
- Decisions get written down so nothing is lost between sessions.
- "I don't know, you pick" is always a valid answer — Claude then chooses and says why.

## Picking this up in a new session

A new chat remembers **nothing**. This document is the memory.

1. Open Claude Code on the Mac inside a kit folder (or share a folder with a cloud session).
2. Say: *"read `docs/superpowers/specs/2026-11-21-pet-yeet-design.md` in my Roblox repo."*
3. Make sure Roblox Studio is open with its MCP server enabled
   (`Assistant → ... → Manage MCP Servers → Enable Studio as MCP server`).

## Next project

***Don't Drop The Egg*** — a physics egg carried through an obstacle course while
other players sabotage you. Deferred on purpose: it needs a populated server to be
fun, which a brand-new game does not have. Revisit once Pet Yeet has an audience.
