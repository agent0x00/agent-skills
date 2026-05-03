---
name: peas-framework
description: Formal agent design using the PEAS framework (Performance, Environment, Actuators, Sensors). Walks through each element with probing questions, produces a structured specification, and maps environment properties to agent architecture. Use when designing an AI agent, analyzing a task environment, or when user mentions "PEAS" or "agent design".
---

# PEAS Framework

Design agents by formally specifying their task environment. The PEAS framework bridges abstract agent concepts and concrete design decisions.

Produce a structured PEAS specification as a markdown document using the template below.

## Workflow

### Step 1 — Elicit the agent concept

Ask the user what agent they want to design. If vague, ask clarifying questions:
- What problem does this agent solve? Who uses it?
- What domain does it operate in?
- What's the rough scope (single task vs ongoing role)?

### Step 2 — Walk through PEAS (one element at a time)

For each element, ask probing questions and capture answers. **Do not proceed to the next element until the current one is sufficiently defined.**

**P — Performance Measure**
Ask: "How do we know the agent is doing a good job?"
Probe for:
- What objective, measurable criteria define success?
- What trade-offs exist (speed vs quality, cost vs accuracy)?
- Can the measure be gamed? (Goodhart's Law: "When a measure becomes a target, it ceases to be a good measure.")
- Negative edge cases: what would look good on the metric but actually be bad?

**E — Environment**
Ask: "What world does the agent operate in?"
Probe for:
- What entities, systems, and data sources exist outside the agent?
- What parts of the environment are fixed vs changing?
- What constraints exist (laws, policies, resource limits)?
- Is the environment fully observable or partially? Deterministic or stochastic?

**A — Actuators**
Ask: "What can the agent do to affect the environment?"
Probe for:
- What actions can the agent take?
- What tools, APIs, or output channels does it have?
- What are the side effects or costs of each action?
- What actions should the agent explicitly be forbidden from taking?

**S — Sensors**
Ask: "How does the agent perceive the environment?"
Probe for:
- What data sources provide input?
- What is the fidelity, latency, and reliability of each sensor?
- What can't the agent sense (blind spots)?
- Is there a difference between raw sensor data and processed perception?

### Step 3 — Classify environment properties

From the PEAS specification, classify the environment along six dimensions and update the spec:

| Property | Continuum | Implication |
|---|---|---|
| Observability | Fully → Partially | Partial observability requires internal state/memory |
| Determinism | Deterministic → Stochastic | Stochastic environments need probabilistic models |
| Episodicity | Episodic → Sequential | Sequential environments require look-ahead/planning |
| Dynamics | Static → Dynamic | Dynamic environments need real-time response |
| Cardinality | Discrete → Continuous | Continuous spaces need function approximation |
| Agency | Single → Multi | Multi-agent requires coordination/negotiation |

### Step 4 — Recommend agent architecture

Based on the classified properties, recommend an architecture:

- **Simple reflex agent** — fully observable, deterministic, static environments
- **Model-based reflex agent** — partially observable (needs internal state)
- **Goal-based agent** — sequential, planning-heavy environments
- **Utility-based agent** — trade-offs between competing objectives
- **Learning agent** — uncertain or changing environments

Explain the recommendation in terms of the PEAS properties.

## Output template

Present the specification as a structured markdown document:

```markdown
# PEAS Specification: [Agent Name]

## Performance Measure
- **Primary:** ...
- **Constraints:** ...
- **Trade-offs:** ...
- **Anti-goals (what success does NOT look like):** ...

## Environment
- **Description:** ...
- **Entities:** ...
- **Properties:**
  - Observability: [Fully | Partially] — because ...
  - Determinism: [Deterministic | Stochastic] — because ...
  - Episodicity: [Episodic | Sequential] — because ...
  - Dynamics: [Static | Dynamic | Semidynamic] — because ...
  - Cardinality: [Discrete | Continuous] — because ...
  - Agency: [Single | Multi] — because ...

## Actuators
| Actuator | Effect | Side effects / Costs |
|---|---|---|
| ... | ... | ... |

## Sensors
| Sensor | Data | Fidelity | Latency |
|---|---|---|---|
| ... | ... | ... | ... |

## Recommended Architecture
**[Architecture type]** — because [explain in terms of PEAS properties].
```

## Reference

See [REFERENCE.md](REFERENCE.md) for:
- Full example specifications (medical diagnosis, automated taxi, web research agent, and more)
- Detailed environment property classifications with examples
- Architecture mapping guide

## Related skills

After the specification is complete, the user may want to:
- `/improve-codebase-architecture` — if the agent will operate in a codebase
- `/to-prd` — to turn the agent spec into a product requirements document
