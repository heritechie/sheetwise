# System Architecture

This document describes the high-level system architecture of Sheetwise.

The architecture is designed to:

- keep the core logic independent from interfaces
- ensure deterministic behavior
- allow multiple interaction surfaces without duplicating state

---

## Architectural Overview

Sheetwise follows a **core-centric architecture**.

All product logic lives in the Sheetwise Core.
External interfaces such as Slack or Web are treated as adapters.

```text
                        ┌───────────────────┐
                        │   Slack Adapter   │
                        │  (Chat Interface) │
                        └─────────┬─────────┘
                                  │
                                  │ intent / commands
                                  ▼
┌──────────────────┐      ┌──────────────────────────────┐      ┌────────────────────┐
│     Web App      │<────>│        Sheetwise Core        │<────>│   AI (via MCP)     │
│ (Preview & Trust)│      │                              │      │ (Model Suggestion) │
└──────────────────┘      │  ┌────────────────────────┐  │      └────────────────────┘
                          │  │    Session Manager     │  │
                          │  ├────────────────────────┤  │
                          │  │    Modeling Engine     │  │
                          │  ├────────────────────────┤  │
                          │  │  Transformer Engine    │  │
                          │  ├────────────────────────┤  │
                          │  │   Output Generator     │  │
                          │  └────────────────────────┘  │
                          └──────────────┬───────────────┘
                                         │
                                         │ validated access
                                         ▼
                               ┌──────────────────┐
                               │   Data Sources   │
                               │  DB / Files /    │
                               │     URLs         │
                               └──────────────────┘

```

---

## Core Principles of the Architecture

### 1. Session-Centric Design

All operations in Sheetwise are scoped to a **session**.

A session represents:

- one data source
- one analytics intent
- one analytics model
- one resulting dataset

Sessions are the single source of truth.
No state is owned by Slack, Web, or AI components.

---

### 2. Interface Independence

The Sheetwise Core does not know:

- whether a request came from Slack or Web
- how the user interface is rendered
- how messages are displayed to users

This allows:

- multiple interfaces to coexist
- interfaces to evolve independently
- future adapters (CLI, API) without refactoring the core

---

### 3. Deterministic Execution Boundary

Only one component is allowed to execute data transformations:
the **Transformer Engine** inside the Core.

AI components:

- may suggest models
- may explain assumptions
- must never execute transformations

Interfaces:

- may request execution
- must not bypass validation

---

## Core Components

### Sheetwise Core

The Sheetwise Core contains all business logic.

Its responsibilities include:

- session lifecycle management
- analytics model validation
- deterministic data transformation
- output artifact generation

The core exposes a stable internal API
consumed by all adapters.

---

### Session Manager

The Session Manager is responsible for:

- creating and loading sessions
- enforcing session state transitions
- attaching user identity to sessions

Allowed state transitions:

- `draft` → `validated`
- `validated` → `ready`

Invalid transitions must be rejected.

---

### Modeling Engine

The Modeling Engine:

- receives analytics intent
- builds or updates the analytics model
- validates model completeness and correctness

The Modeling Engine does not:

- access raw data
- execute transformations

---

### MCP (AI Adapter)

The MCP (Model Context Protocol) adapter:

- translates natural language intent into model suggestions
- receives schema summaries as context
- returns structured model proposals

AI outputs are advisory only.
Final model acceptance is handled by the Core.

---

### Transformer Engine

The Transformer Engine:

- executes the analytics model
- applies flattening, grouping, and aggregation
- produces a flat, tabular dataset

Execution must be:

- deterministic
- reproducible
- free of hidden heuristics

---

### Output Generator

The Output Generator:

- materializes transformation results
- writes outputs to supported formats (e.g. CSV, Excel)
- generates secure access links

Once output is generated,
the responsibility of Sheetwise ends.

---

## Interface Adapters

### Slack Adapter

The Slack Adapter:

- receives user messages
- forwards intent to the Core
- returns concise responses
- delivers links to web previews

The Slack Adapter does not:

- store session state
- validate analytics models
- process data

Slack is treated as a conversational surface only.

---

### Web Application

The Web Application:

- loads sessions by identifier
- renders analytics model summaries
- displays output previews
- provides export actions

The Web App does not:

- perform transformations
- modify models directly
- replace the chat interface

The web is the place where results are reviewed and trusted.

---

## Data Source Access

Data sources are accessed only by the Core.

Supported sources include:

- databases (e.g. MongoDB, SQL)
- files (CSV, JSON)
- file URLs

Credentials must be:

- encrypted at rest
- scoped to sessions
- inaccessible to interface adapters

---

## Security Boundaries

The architecture enforces clear trust boundaries:

- Interfaces cannot access raw data
- AI cannot execute or mutate data
- Only the Transformer Engine touches data
- All outputs are derived from validated models

This separation is intentional
to support auditability and compliance.

---

## Extensibility

The architecture allows for:

- additional interfaces (CLI, API)
- additional output formats
- additional data sources

All extensions must integrate through the Core
and respect session boundaries.

---

## Summary

Sheetwise uses a core-centric, session-based architecture.

By separating intent, execution, and presentation,
the system remains deterministic, auditable, and extensible.

Interfaces may change.
The Core must not.
