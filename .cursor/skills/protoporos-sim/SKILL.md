---
name: protoporos-sim
description: Implement or extend the Protoporos simulation following the README spec. Use when building main.py, adding actions, fixing resolution or adaptation logic, or when the user asks to implement or change the simulation.
---

# Protoporos Simulation Implementation

## When to use this skill

Apply when the user wants to implement, extend, or fix the Protoporos simulation (e.g. "implement main.py", "add a new action", "fix zero-sum", "debug attack resolution").

## Process

1. **Read the spec:** Open README.md and use these sections as the authority:
   - **Simulation Structure** — agent dict fields, modular functions, outcome dict shape
   - **Action Space (V1)** — ATTACK, DEFEND, ALLY
   - **Rule Engine (V1)** — alliance formation, attack resolution order, success formula, defense_bonus, zero-sum on failure
   - **Adaptation Mechanism** — aggression and trust updates from outcome
   - **Simulation Loop** — observe → decide → resolve → adapt → log
   - **Output & Metrics (V1)** — console, protoporos_run.txt, Gini, betrayal count, winner

2. **Implement to match:** Code must follow the README exactly. Do not guess formulas or add mechanics not in the spec.

3. **Constraints:** One `main.py` file; no external dependencies; `random.seed(42)`; total resources constant (zero-sum).

## Outcome dict (for adapt)

Each agent's outcome dict must include at least:

- `attack_target` (id or None)
- `attack_succeeded` (bool)
- `was_attacked_by` (list of ids)
- `was_betrayed_by` (list of ally ids who attacked this agent)
- `allied_with` (list of ids with mutual ALLY this round)
- `chose_defend` (bool)

## Quick reference

- **Resolution order:** Process (attacker_id, target_id) pairs in a fixed order (e.g. lexicographic); use current resources after each transfer.
- **Success prob:** `attacker.aggression * (1 - defender.risk_tolerance) * (1 - defense_bonus)` with defense_bonus 0.2 if DEFEND or alliance with attacker, else 0.
- **Failed attack:** Attacker −5, defender +5 (zero-sum).
- **Betrayal:** Victim's trust in betrayer −0.3. Alliance exists only when both chose ALLY(other).
