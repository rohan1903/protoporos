# Protoporos

**Status:** Planning (not yet implemented)

## Overview

**Protoporos** is a deterministic, zero-sum competitive simulation engine designed to study emergent strategic behavior under constrained rules. (*Deterministic* means: same rules + same random seed → same outcome every time. *Zero-sum* means: no new resources are created; when one agent gains, others lose.)

It models a closed world of adaptive agents competing for dominance through aggression, alliances, and defensive strategies. The system is built to explore how rule structures shape power accumulation, betrayal dynamics, and systemic stability.

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

### Agents

Each agent contains:

* `id`
* `resources`
* `aggression_level (0–1)`
* `risk_tolerance`
* `trust_map (agent → trust score)`
* `memory (last N interactions)`

### Agent Interface

```
observe(state)
decide()
update(outcome)
```

* `observe` reads the current environment snapshot.
* `decide` selects an action.
* `update` adjusts internal parameters after resolution.

---

## Action Space (V1)

Minimal action set:

1. `ATTACK(target)`
2. `DEFEND`
3. `ALLY(target)`

No additional complexity in Version 1.

---

## Rule Engine (V1)

### Attack Resolution

* Success probability based on attacker aggression vs defender state.
* Successful attack transfers resources.
* Failed attack incurs cost.

### Defense

* Reduces attack success probability.
* Minor resource upkeep cost.

### Alliance

* Mutual defense bonus.
* If betrayal occurs, trust penalty applied.

All formulas are explicitly defined and configurable.

---

## Adaptation Mechanism

After each round, agents update parameters:

* Successful aggression → increase aggression slightly
* Failed aggression → decrease aggression
* Profitable alliance → increase alliance preference
* Betrayal experienced → reduce trust toward betrayer
* Repeated losses → adjust risk tolerance

Adaptation is bounded and incremental to avoid instability spikes.

---

## Simulation Loop

For each round:

1. Agents observe state.
2. Agents choose actions.
3. Environment collects and resolves all actions simultaneously.
4. Resources updated.
5. Trust maps updated.
6. Metrics recorded.
7. Agents update strategy.

---

## Termination Condition (V1)

Version 1 uses:

**Fixed Round Limit**
(e.g., 200 rounds)

Winner = agent with highest resource count at termination.

---

## Metrics & Analysis

The simulation tracks:

* Resource inequality (e.g., Gini coefficient)
* Dominance emergence rate
* Alliance duration distribution
* Betrayal frequency
* Agent survival time
* Volatility of power shifts

Logs are stored for post-simulation analysis.

---

## Version 1 Constraints

To prevent scope explosion:

* 10 agents
* 3 actions only
* Linear adaptation rules
* No UI
* CLI-based execution
* Logged output to file
* Deterministic seed control

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

* **Prerequisites:** (Fill in once you choose a language—e.g. Node.js, Python 3.x.)
* **Run the simulation:** (e.g. `npm start` or `python run_simulation.py`—add when you implement it.)
* **Where to start building:** A folder structure blueprint, technical architecture doc, or TypeScript/type definitions will help; add them when you begin implementation.
