# PEAS Framework Reference

## Environment Properties (detailed)

### 1. Observability: Fully vs Partially Observable

An environment is **fully observable** if the agent's sensors give it access to the complete state of the environment at every point in time. **Partially observable** if sensors only provide a subset.

- **Fully:** Chess (all pieces visible on the board), a sort function (full array in memory)
- **Partially:** Poker (opponents' hidden cards), web search (unindexed pages)

**Architecture impact:** Partial observability forces the agent to maintain **internal state** — a model of the world that tracks what it has inferred about hidden variables.

### 2. Determinism: Deterministic vs Stochastic

An environment is **deterministic** if the next state is completely determined by the current state and the agent's action. **Stochastic** if there is randomness or uncertainty in state transitions.

- **Deterministic:** A calculator (2+2 always equals 4), a well-defined REST API with no side effects
- **Stochastic:** A stock market, a dice game, predicting user behavior

**Architecture impact:** Stochastic environments require **probabilistic models** and often utility-based reasoning to handle uncertainty.

### 3. Episodicity: Episodic vs Sequential

**Episodic** means each agent action happens in a discrete "episode" — the next action does not depend on previous ones. **Sequential** means current decisions affect future options.

- **Episodic:** Classifying a single image (results are independent), spam detection per email
- **Sequential:** Chess (a move changes the board for all future moves), navigation (a wrong turn changes the route)

**Architecture impact:** Sequential environments need **planning** and **look-ahead**. Past decisions constrain future options.

### 4. Dynamics: Static vs Dynamic (vs Semidynamic)

**Static** means the environment doesn't change while the agent is deciding. **Dynamic** means it does. **Semidynamic** means the environment itself is static but the agent's performance score changes while it deliberates (e.g., a chess clock).

- **Static:** Solving a Sudoku puzzle (board unchanged while you think)
- **Dynamic:** Driving a car (traffic moves while you decide)
- **Semidynamic:** Competitive chess with a clock (board static, score degrading)

**Architecture impact:** Dynamic environments require **real-time responses** or at least a time-bound decision loop. The agent must balance deliberation speed against decision quality.

### 5. Cardinality: Discrete vs Continuous

**Discrete** means a finite number of distinct states, actions, or time steps. **Continuous** means infinite (real-valued) possibilities.

- **Discrete:** Chess (finite board positions), a text-based menu (finite choices)
- **Continuous:** Steering a car (infinite steering angles), image recognition (pixel values are continuous)

**Architecture impact:** Continuous spaces need **function approximation** or **discretization** strategies. Tabular methods won't work.

### 6. Agency: Single-agent vs Multi-agent

**Single-agent** means the agent operates alone. **Multi-agent** means other agents (cooperative or competitive) exist in the environment.

- **Single:** A spell-checker, a thermostat
- **Multi:** Two-player games (competitive), warehouse robots (cooperative), auctions (mixed)

**Architecture impact:** Multi-agent environments need **communication protocols**, coordination mechanisms, and possibly game-theoretic reasoning.

---

## Architecture Mapping Guide

| Architecture | Best for | Key requirement |
|---|---|---|
| **Simple reflex** | Fully observable, deterministic, static | Condition-action rules map directly from sensor to actuator |
| **Model-based reflex** | Partially observable | Maintains internal state to track unobserved variables |
| **Goal-based** | Sequential, clear success criteria | Can search/plan to reach a goal state from current state |
| **Utility-based** | Conflicting objectives, trade-offs | A utility function quantifies how "good" a state is |
| **Learning** | Uncertain or changing environment | Experience data to improve performance over time |

Agents are often **hybrid**: a goal-based agent may also use a reflex layer for emergencies, and a learning agent may overlay any of the above architectures.

---

## Worked Examples

### Example 1: Medical Diagnosis Assistant

```
Performance:   Maximize patient health outcomes, minimize costs, reduce legal liability
Environment:   Patient, hospital staff, medical records, lab equipment, test results
Actuators:     Display questions, recommend tests, output diagnoses, prescribe treatments
Sensors:       Keyboard input, voice input, medical record API, lab result API
```

**Properties & Architecture:**
- **Partially observable** — cannot directly observe the patient's internal physiology; must infer from test results and symptoms
- **Stochastic** — symptoms don't map deterministically to diseases; treatments have probabilistic outcomes
- **Sequential** — a diagnosis informs treatment, which informs follow-up tests
- **Semidynamic** — patient condition degrades while diagnosing (performance score changes)
- **Continuous** — vital signs are real-valued; test results have continuous ranges
- **Multi-agent** (collaborative) — works with doctors, nurses, and specialists

→ **Recommended: Utility-based agent** — must weigh treatment efficacy against side effects, cost against urgency. Overlaid with a **learning component** to improve diagnostic accuracy from outcomes.

---

### Example 2: Automated Taxi

```
Performance:   Safety, speed, legality, comfort, profit, fuel efficiency
Environment:   Roads, traffic, pedestrians, weather, traffic laws, passengers
Actuators:     Steering wheel, accelerator, brake, turn signals, horn, display
Sensors:       Cameras, LiDAR, GPS, accelerometer, odometer, engine sensors
```

**Properties & Architecture:**
- **Partially observable** — can't see around corners or inside other vehicles
- **Stochastic** — pedestrian behavior, traffic patterns, weather are unpredictable
- **Sequential** — every turn affects route; hard braking affects passenger comfort for the whole ride
- **Dynamic** — traffic and pedestrians move while the car decides
- **Continuous** — steering angle, speed, position are real-valued
- **Multi-agent** (mixed) — other drivers (competitive), passengers (cooperative), pedestrians (neutral)

→ **Recommended: Hybrid architecture.** Goal-based for route planning (find path to destination), **utility-based** for moment-to-moment decisions (balance speed vs safety vs comfort), **model-based reflex** for partially observable blind spots (maintain internal map of road geometry), and a **reflex layer** for emergency braking (hardcoded: if obstacle imminent → brake).

---

### Example 3: Vacuum Cleaner

```
Performance:   Amount of dirt removed, cleaning time, energy used, area covered
Environment:   Room layout, furniture, dirt distribution, floor type
Actuators:     Wheels (movement), vacuum motor, brush rollers, dirt sensor actuator
Sensors:       Contact/bump sensors, dirt detectors, wheel encoders, IR/laser ranging
```

**Properties & Architecture:**
- **Partially observable** — doesn't know full room layout without mapping
- **Deterministic-ish** — wheel movement is mostly predictable; dirt detection is noisy
- **Sequential** — cleaning path affects coverage efficiency
- **Dynamic** (mild) — furniture may be moved but generally static during cleaning
- **Continuous** — position in room is continuous
- **Single-agent** — operates alone

→ **Recommended: Model-based reflex agent.** Maintains a map (internal state) for coverage tracking. When dirt detected, switch to intensive cleaning mode (condition-action rule). For budget models: **simple reflex** with random bouncing (no internal state needed).

---

### Example 4: Web Research Agent

```
Performance:   Relevance & accuracy of results, comprehensiveness, response time, source diversity
Environment:   Web (pages, APIs, search engines), knowledge domains, user intent
Actuators:     Search engine queries, URL fetches, file writes, result formatting
Sensors:       HTTP response parser, HTML renderer, search result API, user query input
```

**Properties & Architecture:**
- **Partially observable** — massive parts of the web are unseen; must decide what to explore
- **Stochastic** — search results vary; pages change; APIs may fail
- **Sequential** — a search query's results inform the next query
- **Dynamic** — pages change, search indexes update, rate limits change
- **Discrete** — pages, queries, and results are discrete items
- **Single-agent** — the research agent acts alone

→ **Recommended: Goal-based agent with learning.** The goal (answer the research question) drives query formulation. A **learning component** tracks which queries and sources produce good results, improving over time. Must handle partially observable state through iterative exploration.

---

### Example 5: Code Review Assistant

```
Performance:   Bug detection rate, false positive rate, review speed, helpfulness of feedback
Environment:   Source code repository, PR diff, CI results, coding standards, reviewer
Actuators:     PR comments, approval/rejection, inline suggestions, report generation
Sensors:       Git diff parser, AST parser, linter output, test results, style guide
```

**Properties & Architecture:**
- **Partially observable** — can't see the author's intent or full runtime behavior
- **Deterministic** — code analysis tools produce consistent results
- **Episodic** — each review is independent (though patterns emerge across PRs)
- **Static** — the PR diff doesn't change during the review
- **Discrete** — lines of code, comments, and files are discrete
- **Multi-agent** (collaborative) — works alongside human reviewers

→ **Recommended: Model-based reflex + utility.** Condition-action rules for obvious patterns (lint errors → flag). **Utility-based** for nuanced decisions (is this a real bug or a false positive? Weigh severity against false-alarm cost). A **learning component** can tune detection thresholds from accepted/rejected feedback.

---

### Example 6: Stock Trading Agent

```
Performance:   Profit, risk-adjusted return (Sharpe ratio), drawdown, latency
Environment:   Exchanges, order book, news feeds, economic data, other traders
Actuators:     Buy/sell orders, order cancellations, position adjustments
Sensors:       Price feed API, order book depth, news API, economic indicators
```

**Properties & Architecture:**
- **Partially observable** — can't see other traders' strategies or pending orders
- **Stochastic** — price movements are random walks with signal
- **Sequential** — every trade affects portfolio and future flexibility
- **Dynamic** — prices change in milliseconds while the agent decides
- **Continuous** — prices, volumes, and time are continuous
- **Multi-agent** — competing traders, market makers, regulators

→ **Recommended: Learning agent** — must adapt to changing market regimes. **Utility-based** for risk/reward trade-offs. Requires low-latency reflex layer for market-making (if signal detected → execute). Partially observability demands internal state modeling order flow.

---

### Example 7: Personal Calendar Assistant

```
Performance:   Scheduling accuracy, user satisfaction, conflict avoidance, response time
Environment:   User's calendar, contacts, meeting rooms, time zones, email
Actuators:     Create/update/delete events, send invites, suggest times, send reminders
Sensors:       Calendar API, email parser, user preference input, contact list
```

**Properties & Architecture:**
- **Fully observable** for the calendar data; **partially** for user preferences and availability
- **Deterministic** — calendar operations produce predictable results
- **Sequential** — scheduling one meeting affects availability for others
- **Static** — calendar entries are stable during reading (changes happen through the agent)
- **Discrete** — events, time slots, attendees are discrete entities
- **Multi-agent** (cooperative) — interacts with other attendees' calendars

→ **Recommended: Goal-based agent.** The goal (find a time that satisfies constraints) drives a search over available slots. Simple reflex for routine operations (double-booking detected → alert). Maintains an internal model of user preferences for proactive suggestions.

---

## Running PEAS on an existing agent

Use PEAS to analyze an existing agent by working backwards:

1. **Observe behavior** — What does the agent actually do? (Actuators)
2. **Identify inputs** — What information does it consume? (Sensors)
3. **Infer objectives** — What is it optimizing? (Performance Measure)
4. **Map the context** — What world does it navigate? (Environment)

This reverse-PEAS is useful for:
- Debugging agent failures (misalignment between intended and actual PEAS)
- Identifying architectural mismatches
- Documenting orphaned or undocumented agents
- Evaluating whether an agent should be replaced by a simpler approach

## Common pitfalls

| Pitfall | Example | Fix |
|---|---|---|
| **Confusing actuators with performance** | "The agent's job is to call the search API" (that's an actuator, not a goal) | Performance should answer "why" not "how" |
| **Overlapping measures** | "Maximize accuracy and minimize errors" (same thing) | Each measure should be independent |
| **Unmeasurable performance** | "Be helpful" (vague) | Define concrete criteria: "User completes task in under X steps" |
| **Ignoring trade-offs** | "Speed and accuracy" without noting they conflict | Explicitly state how to balance conflicting measures |
| **Forgetting sensors** | Assuming the agent "just knows" something | Every input must come through a sensor; missing sensors = blind spots |
| **Self-referential performance** | "Minimize time spent thinking" when the agent controls its own reasoning | Performance should evaluate environment outcomes, not agent internals |
| **Environment scope creep** | Including things the agent never interacts with | If the agent can't affect or sense it, it's not part of the task environment |
