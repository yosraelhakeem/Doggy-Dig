# Good dig

A cozy digging puzzle. Minesweeper's structure, inverted: the buried things
are rewards, not hazards.

`index.html` is a complete working prototype — no build step, no
dependencies. Open it in a browser and it runs.

---

## The idea

Classic Minesweeper ends in negative space: you win by *not* touching things,
and the finished board shows nothing you made. Good dig flips that. A dog
digs up a yard looking for buried bones. The win screen is a pile of bones
you found.

**Audience:** adults, skewing 35+, the Candy Crush demographic. Short
sessions, often interrupted, on a phone. Not children (that triggers COPPA
and kills rewarded video), and not speedrunners — the existing Minesweeper
ecosystem is entirely built for people optimising 3BV/s, and none of them
want a cozy dog. That gap is the whole opportunity.

---

## Rules

One tool, two gestures:

| Action | Gesture | Outcome |
|---|---|---|
| Dig | tap | reveals dirt and a number, or chips a bone |
| Claim | hold 550ms | claims a bone, or costs a life if wrong |

- Numbers count adjacent bones, exactly like Minesweeper.
- Three lives. Both mistakes cost one.
- Digging onto a bone chips it: costs a life, and the bone can no longer be
  claimed, but it still counts toward finishing.
- **Win** when every bone is claimed or chipped. **Perfect** when all are
  claimed and none chipped.
- No hard game over that loses your board. Running out of lives ends the run,
  but nothing explodes and nothing is taken away.
- **First dig is always safe** — a bone is relocated if the opening tap would
  hit one. Already implemented.

---

## Decisions already made

**No "?" marker.** Removed deliberately to keep one gesture threshold. Worth
revisiting if the board ever goes past 8×8 — at that size players can't hold
every deduction in their head, and a wrong claim costs a life here, so the
pressure to mark tentatively is higher than in classic Minesweeper.

**Timer is tracked but hidden.** A visible clock is the most anti-cozy
element available and conflicts with a design that rewards thinking. Record
it, show it on the win card, offer a toggle in settings, default off.

**Width caps at 10 columns.** This is a thumb constraint, not a difficulty
one. Harder boards get *taller*, not wider. Classic 9×9 / 16×16 / 30×16 sizes
were designed for a mouse and don't transfer.

**Bone density must scale with area.** A bigger board with the same bone
count is *easier*, not harder:

| Board | Bones | Density |
|---|---|---|
| classic beginner 9×9 | 10 | 12.3% |
| classic intermediate 16×16 | 40 | 15.6% |
| classic expert 30×16 | 99 | 20.6% |
| **current 7×7** | **8** | **16.3%** |
| 10×10 | 12 | 12.0% |
| 12×12 | 20 | 13.9% |

**Difficulty ramps on performance, not level count.** After three
consecutive boards cleared with no chips, *offer* the next size up. Offer,
never force.

---

## The most important thing left to build

**A solvability filter.** Random bone placement regularly produces boards
where two configurations are equally consistent with the clues and the player
must guess. In a game where a wrong guess costs a life, that is the single
most likely reason someone deletes the app.

Generate a board, run a deduction solver, regenerate if the solver gets
stuck. There's a `TODO` marking the spot in `index.html`. Combinatorics are
not a constraint — 10×10 with 12 bones has ~10^15 arrangements, so rejecting
most of them costs nothing.

This matters more than the art.

---

## Roadmap, roughly in order

1. Solvability filter
2. Board sizes and the performance-based ramp
3. Persistence — save board state so an interrupted session resumes exactly
4. Sound, haptics on claim
5. Real dog art (see below)
6. Points and a personal best, offline
7. Leaderboard, only if step 6 proves people play daily without one

## Explicitly not building yet

- Collections, rare fossils, mystery finds, legendary bones
- Multiple dogs and dog personalities
- Locations and themed sets
- Streaks — they only ever punish. Cumulative total digs gives the same
  satisfaction with none of the guilt, and doesn't break when someone goes on
  holiday.
- Global leaderboards. If leaderboards happen, use weekly leagues of ~30
  players. Rank 400,000 of 2 million is demotivating and it's what almost
  everyone sees.

---

## Art

The dog and bone in `index.html` are hand-written SVG placeholders. They are
deliberately simple and meant to be replaced.

The dog is the emotional feedback channel and the thing that would take this
from cute to charming — he needs to *do* something, not just change
expression. Four states are already wired: `idle`, `happy`, `ouch`, `sad`.

[Rive](https://rive.app) is the right tool for this: animation with state
machines, runtimes for web and React Native. Rive does not build apps — it
produces the animated dog, and the app is built separately.

Font: the CSS asks for Nunito or Quicksand and falls back to a rounded system
face. Add a real webfont before shipping.

---

## Shipping

No backend needed for the offline version. Path of least resistance:

1. Iterate on `index.html` directly
2. Wrap with [Capacitor](https://capacitorjs.com) for an Android build
3. Supabase for a leaderboard, only if and when it's earned

Sanity check before any of that: put it somewhere a link reaches, send it to
ten people, and see whether anyone plays it twice.

---

## Honest context

Casual puzzle discovery on Google Play is brutal and ad revenue is cents per
daily user. Cat Sweeper — a competent cozy Minesweeper reskin — has roughly
a hundred downloads. That's the median outcome, not bad luck.

Build this because it's small enough to finish and it's fun. Treat any money
as a surprise.
