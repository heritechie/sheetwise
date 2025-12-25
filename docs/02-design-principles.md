# Design Principles

This document defines the core design principles of Sheetwise.
These principles act as guardrails for all product, UX, and technical decisions.

If a proposed feature violates one of these principles,
it should not be implemented.

---

## Principle 1 — Human-Consumable First

Sheetwise is designed to produce outputs that are directly consumable
by humans before being consumed by machines.

### What this means

- The primary output of Sheetwise is a flat, tabular dataset
- Outputs must be readable in spreadsheets without additional tooling
- Column names, data types, and granularity must be explicit and stable

### What this avoids

- Nested or semi-structured outputs
- Machine-optimized formats that require explanation
- Outputs that only make sense after visualization

> If a dataset cannot be opened and understood in a spreadsheet,
> it is not considered ready.

---

## Principle 2 — Deterministic Over Smart

Sheetwise prioritizes deterministic behavior over intelligent or adaptive behavior.

Given the same data source and the same analytics model,
Sheetwise must always produce the same output.

### What this means

- No hidden heuristics in data transformation
- No AI-driven execution or decision-making
- All transformations are explicit and reproducible

### Role of AI

AI may assist in:

- translating user intent into a suggested analytics model
- explaining assumptions and trade-offs

AI must never:

- execute data transformations
- decide final outputs
- alter results dynamically

> Sheetwise does not try to be clever.
> It tries to be correct.

---

## Principle 3 — Readiness, Not Interpretation

Sheetwise stops at preparing data for analysis.
It does not interpret, explain, or draw conclusions from the data.

### What this means

- Sheetwise produces BI-ready datasets, not insights
- Visualization, storytelling, and decision-making happen downstream
- Responsibility for interpretation remains with humans and BI tools

### What Sheetwise explicitly avoids

- Automated insights or summaries
- Charts or dashboards
- Recommendations or conclusions

> Sheetwise prepares the ground.
> It does not decide what grows on it.

---

## Principle 4 — Session as the Source of Truth

All work in Sheetwise is scoped to a session.

A session represents a single, coherent effort to prepare a dataset.

### What this means

- Sessions are platform-agnostic
- Slack, Web, or other interfaces are only adapters
- All state lives in the Sheetwise Core

This ensures:

- reproducibility
- auditability
- consistency across interfaces

---

## Principle 5 — Explicit Over Implicit Modeling

Analytics intent must be expressed as an explicit model.

### What this means

- Time grain, metrics, and dimensions are always defined
- Defaults must be visible and overridable
- Hidden assumptions are not allowed

If a user cannot describe the model explicitly,
the dataset is not ready.

---

## Anti-Goals

Sheetwise is explicitly **not** designed to be:

- a BI tool
- a data warehouse
- a streaming analytics system
- an automated decision engine
- a conversational AI product

Features that move Sheetwise toward these goals
should be rejected.

---

## Design Review Checklist

Before implementing a feature, ask:

- Does this improve human consumability?
- Does this preserve deterministic behavior?
- Does this stop before interpretation?
- Does this respect session boundaries?
- Does this make the analytics model more explicit?

If the answer to any of these is **no**,
the feature should be reconsidered.

---

## Closing Note

These principles are intentionally restrictive.

They exist to protect Sheetwise from scope creep,
over-automation, and loss of trust.

Sheetwise succeeds by doing less — but doing it reliably.
