# Protoporos

**Status:** Planning (not yet implemented)

## Overview

**Protoporos** is a deterministic, zero-sum competitive simulation engine designed to study emergent strategic behavior under constrained rules.

It models a closed world of adaptive agents competing for dominance through aggression, alliances, and defensive strategies. The system is built to explore how rule structures shape power accumulation, betrayal dynamics, and systemic stability.

**Beginner-friendly:** Version 1 is a single Python file (`main.py`), no external packages—only built-in Python 3 and `random` with a fixed seed. Run it, read the console and the log file, then tweak the rules to see how behavior changes.

---

## Core Objective

Protoporos aims to answer:

* How do simple rules generate complex power dynamics?
* Under what conditions does dominance emerge?
* When do alliances stabilize vs collapse?
* How does adaptive behavior alter long-term system equilibrium?
* What rule structures produce stable vs chaotic systems?

---

## System Characteristics

### Closed World

* Fixed total resource pool
* No external growth
* Zero-sum competition
* Resource transfer is redistribution only

### Deterministic Core

* Seeded randomness for reproducibility
* Explicit rule engine
* No hidden state transitions

### Adaptive Agents

* Agents modify behavioral parameters based on outcomes
* Strategy evolves through incremental reinforcement-like updates
* No neural networks or external AI dependencies

---

## Simulation Structure

### Agents (V1)

**5 agents**, each represented as a dict with:

| Field | Meaning |
|-------|--------|
| `id` | Integer 0–4 |
| `resources` | Current resources; start with **exactly 20 each** (100 total pool) |
| `aggression_level` | Float in [0, 1]; initial 0.5 |
| `risk_tolerance` | Float; initial 0.5 |
| `trust_map` | Dict `{other_id: trust_score}`; initial 0.5 for each other agent |

### Modular functions (implementation API)

| Function | Purpose |
|----------|--------|
| `observe()` | Returns current global state (resources, actions, alliances) for agents to read. |
| `decide(agent)` | Returns one action: `ATTACK(target_id)`, `DEFEND`, or `ALLY(target_id)`. |
| `resolve_actions(actions_list)` | Applies the rules below in a fixed order; returns **outcome** per agent. |
| `adapt(agent, outcome)` | Updates `aggression_level` and `trust_map` from this round’s outcome. |
| `compute_gini(resources_list)` | Gini coefficient over the 5 resource values (standard formula). |

**Outcome (per agent)** — dict passed into `adapt()`, with at least:

* `attack_target` — id attacked, or None
* `attack_succeeded` — bool
* `was_attacked_by` — list of attacker ids
* `was_betrayed_by` — list of ally ids who attacked this agent this round
* `allied_with` — list of ids with mutual ALLY this round
* `chose_defend` — bool

---

## Action Space (V1)

Each agent chooses exactly one action per round:

1. **`ATTACK(target_id)`** — try to take resources from that agent.
2. **`DEFEND`** — reduce success chance of attacks against you this round.
3. **`ALLY(target_id)`** — propose/confirm alliance with that agent (see below).

---

## Rule Engine (V1)

### Alliance formation

An **alliance between A and B exists in a round** only if **both** chose `ALLY(other)` that round. When it exists, both get a **+0.2 defense bonus** against any attacker. If A had an alliance with B and A chose `ATTACK(B)`, that is a **betrayal**; B’s trust in A drops by **0.3** (one penalty).

### Attack resolution (order and formula)

1. Collect all actions; build the list of (attacker_id, target_id) pairs from ATTACKs.
2. **Process pairs in a fixed order** (e.g. by (attacker_id, target_id)). For each pair, use **current** resources (after earlier transfers this round).

**Success probability** for one attack:

`attacker.aggression × (1 − defender.risk_tolerance) × (1 − defense_bonus)`

* `defense_bonus` = **0.2** if the target either chose **DEFEND** or had an **alliance with the attacker** this round; otherwise **0**.

**On success:** Transfer **10% of the target’s current resources** from target → attacker.

**On failure (zero-sum):** Attacker loses **5** resources, defender **gains 5** (so total resources stay constant). If you add a cap per agent later, cap the defender’s gain so you don’t create resources.

---

## Adaptation Mechanism

After each round, **adapt(agent, outcome)** updates:

| Event | Change |
|-------|--------|
| Own attack succeeded | +0.05 aggression (cap at 1) |
| Own attack failed | −0.05 aggression (floor 0) |
| Was betrayed (ally attacked me) | −0.3 trust toward that betrayer |
| Had mutual alliance this round | +0.05 trust toward that ally |

All changes are small and bounded so the simulation stays stable.

---

## Simulation Loop

Each round:

1. **Observe** — each agent gets current state (resources, etc.).
2. **Decide** — each agent returns one action (`ATTACK` / `DEFEND` / `ALLY`).
3. **Resolve** — process all attacks in fixed order; apply resource transfers; build **outcome** per agent.
4. **Adapt** — call `adapt(agent, outcome)` for each agent.
5. **Log** — write round summary (resources, any betrayal) to console and log file.

Use **`random.seed(42)`** at startup so the same run is reproducible.

---

## Termination (V1)

**200 rounds**, then stop.

**Winner** = agent with highest resources. If tied, **lowest id** wins.

---

## Output & Metrics (V1)

* **Console:** Round summaries, then at the end: Gini coefficient, total betrayal count, final resources per agent, winner.
* **Log file:** `protoporos_run.txt` — full run (e.g. same as console, or one line per round).

**Gini coefficient** — use the standard formula over the list of 5 resource values (e.g. mean absolute difference over 2×mean) to measure inequality.

---

## Version 1 Constraints

Keep scope small:

* **5 agents**, one `main.py` file
* **3 actions only** (ATTACK, DEFEND, ALLY)
* **No external dependencies** — Python 3 built-ins + `random` with seed
* **No UI** — console + `protoporos_run.txt` only
* **Deterministic** — same seed → same outcome

---

## Future Extensions

* Hidden information layers
* Multi-agent coalitions
* Information asymmetry
* Resource taxation models
* Shock events (rule mutations mid-simulation)
* Evolutionary agent replacement
* Visualization dashboard
* Experiment runner for parameter sweeps

---

## Engineering Goals

* Clean separation of Agent and Environment
* Deterministic simulation core
* Modular rule engine
* Parameter-driven configuration
* Experiment reproducibility
* Clear logging & observability

---

## Non-Goals (V1)

* Multiplayer UI
* Real-time graphics
* Machine learning models
* External AI APIs
* Monetization
* Game polish

---

## Why Protoporos Exists

Protoporos is an exploration of:

* Incentive design
* Emergent systems
* Strategic adaptation
* Power concentration dynamics
* Stability vs collapse under rule constraints

It is built to develop systems modeling capability and disciplined experimental thinking.

---

## When You Return to This Project

* **Prerequisites:** Python 3.x (no extra packages).
* **Run the simulation:** `python main.py` (once `main.py` exists).
* **Where to start building:** Implement the functions in order: `observe`, `decide` (with simple rules: e.g. attack low-trust/low-resource if aggressive; ally with high-trust if tolerant; else DEFEND), then `resolve_actions`, then `adapt`, then `compute_gini`. Wire them in a loop for 200 rounds and log to console + `protoporos_run.txt`.
