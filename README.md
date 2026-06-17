# 🎱 Bayes Billiard Competition

A two-player browser game that teaches Bayesian reasoning through a billiard-table estimation challenge. Players use clue ball indicators to narrow down the hidden cue ball's position, manage a budget, and compete over multiple rounds.

▶ **[Play the game](https://cve-1990-0001.github.io/Bayes-Billiard-Competition/)**

---

## Overview

Each player has their own billiard table (0–90 cm wide). At the start of every round a **white cue ball** is secretly placed at a random integer position on the table. Players cannot see where it landed — they must infer its location from **red clue ball indicators** and their Bayesian intuition.

---

## Game Rules

### Setup
- Enter names for Team 1 and Team 2 (defaults: *Team Alpha* / *Team Beta*).
- Each team starts with **€100** budget.
- Click **Start Competition** to begin.

### Each Round — three phases

#### Phase 1 · Play (Estimate the range)
1. **5 clue balls** are automatically thrown onto your table. For each ball you see only its position relative to the hidden cue ball:
   - 🔴 **LEFT** — the clue ball landed to the left of the cue ball.
   - 🔵 **RIGHT** — the clue ball landed to the right of the cue ball.
   - 🟡 **HIT** — the clue ball landed exactly on the cue ball.
2. **Buy extra clue balls** (optional) to gain more information:
   - First extra ball costs **€5**.
   - Cost doubles on every throw globally (regardless of round): €5, €10, €20, €40, ...
3. **Submit a range** `[min, max]` (integers, 0–90 cm, with `min ≤ max`) that you believe contains the cue ball.
4. The hidden cue position and all clue ball positions stay fixed for each team across rounds; rounds are for refining your interval.

#### Phase 2 · Scoring
Your range is evaluated:
| Outcome | Condition | Budget change |
|---|---|---|
| **Precise & correct** | Cue ball is inside your range **and** range width is within this round's max limit | **+€0 to +€200** (narrower = more) |
| **Too wide or wrong** | Range is wider than this round's max limit, or cue ball is outside your range | **−30% of current budget** |

Special point-range rule:
- If `min = max`, the range is treated as an exact guess.
- If that point matches the cue position, it gets exact-guess credit (`+€200`) and counts for endgame winner checks.
- If it does not match, normal wrong-range penalty still applies.

Round max range width:
```
maxWidth(round) = 60 × 0.7^(round − 1) cm
```

Budget reward formula (when range is within maxWidth and correct):
```
precision = (maxWidth − width) / maxWidth
budgetGain = round(200 × precision)
```

Score formula (when range is within maxWidth and correct):
```
score = 50 + precision × 150   → rounds to nearest integer (50–200)
```

A team whose budget drops to **€0 or below** is **eliminated**.

#### Phase 3 · Guess (exact position)
After scoring, each active team can guess the **exact integer position** of the cue ball (unless already solved via `min = max`):
- A **correct guess** awards **+€200** and immediately ends the game — that team wins.
- An incorrect guess gives no reward or penalty.

### End of Game
The game ends when:
- A team guesses the exact cue ball position correctly → **that team wins**.
- A team finds the exact cue position with `min = max` → treated the same as an exact guess for winning logic.
- If both teams find the exact cue position in the same round (via `min = max`, exact guess, or both), the team with higher effective budget wins; if budgets are equal, both teams win.
- All remaining teams are eliminated → **draw** (both ran out of budget).
- One team is eliminated while the other survives → **surviving team wins**.

---

## Scoring Details

| Situation | Points / Budget impact |
|---|---|
| Correct range at width = 0 cm | +€200 budget, 200 pts |
| Correct range at max allowed width | +€0 budget, 50 pts |
| Correct range narrower than limit | Reward increases linearly with precision |
| Wrong range or width above round limit | −30% of budget |
| Exact position guess (`Guess` or `min = max`) | +€200 exact score bonus, game-ending logic applies |

---

## The Bayesian Connection

This game is a hands-on demonstration of **Bayesian inference**:

- **Prior** — before seeing any clue balls, the cue ball could be anywhere on the table (uniform distribution over 0–90).
- **Likelihood** — each clue ball indicator (`LEFT`, `RIGHT`, `HIT`) is evidence that updates your belief about the cue ball's location.
- **Posterior** — after observing all indicators, your posterior belief is concentrated between the rightmost `LEFT` ball and the leftmost `RIGHT` ball (or exactly at a `HIT` ball).

The optimal Bayesian range to submit is the interval `[max LEFT position + 1, min RIGHT position − 1]` based on all observed clue balls. Buying extra clue balls narrows this posterior — but costs budget, so there is a trade-off between information and resources.

---

## Technical Notes

- Pure HTML/CSS/JavaScript — no dependencies, no build step.
- Open `index.html` in any modern browser to play locally.
- Table width: **90 cm** (positions 0–90, inclusive — 91 possible integer values).
- Extra ball cost per throw: `€5 × 2^(totalExtraThrowsSoFar)`.
