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
   - Round 1 costs **€5** per extra ball.
   - Cost doubles every round: €10 in Round 2, €20 in Round 3, €40 in Round 4, and so on.
3. **Submit a range** `[min, max]` (integers, 0–90 cm) that you believe contains the cue ball.

#### Phase 2 · Scoring
Your range is evaluated:
| Outcome | Condition | Budget change |
|---|---|---|
| **Precise & correct** | Cue ball is inside your range **and** range width ≤ 60 cm | **+€50 to +€200** (narrower = more) |
| **Wide or wrong** | Range is wider than 60 cm, or cue ball is outside your range | **−30% of current budget** |

Precision score formula (when range is ≤ 60 cm and correct):
```
score = 50 + (60 − width) / 60 × 150   → rounds to nearest integer (50–200)
```

A team whose budget drops to **€0 or below** is **eliminated**.

#### Phase 3 · Guess (exact position)
After scoring, each active team can guess the **exact integer position** of the cue ball:
- A **correct guess** awards **+€200** and immediately ends the game — that team wins.
- An incorrect guess gives no reward or penalty.

### End of Game
The game ends when:
- A team guesses the exact cue ball position correctly → **that team wins**.
- All remaining teams are eliminated → **draw** (both ran out of budget).
- One team is eliminated while the other survives → **surviving team wins**.

---

## Scoring Details

| Situation | Points / Budget impact |
|---|---|
| Correct range, width = 0 cm (exact!) | +€200 |
| Correct range, width = 30 cm | +€125 |
| Correct range, width = 60 cm | +€50 |
| Wrong range or width > 60 cm | −30% of budget |
| Exact position guess | +€200, game over |

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
- Extra ball cost per round: `€5 × 2^(round − 1)`.
