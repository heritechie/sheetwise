# Session Model

This document defines the session model used by Sheetwise.

A session is the fundamental unit of work in Sheetwise. All interactions, transformations, and outputs are scoped to a session.

---

## What Is a Session

A session represents a single, coherent effort to prepare one dataset for analysis.

It encapsulates:

- the selected data source
- the analytics intent expressed by the user
- the resulting analytics model
- the transformation state
- the generated output artifacts

A session exists independently of any interface.

---

## Why Sessions Exist

Sessions exist to solve three core problems:

1. **Consistency**  
   Ensuring that analytics intent and execution remain aligned.

2. **Reproducibility**  
   Allowing the same dataset to be regenerated deterministically.

3. **Interface Independence**  
   Enabling multiple interfaces (Slack, Web, future adapters)
   to operate on the same underlying context.

---

## Session as the Source of Truth

In Sheetwise, the session is the single source of truth.

- Slack does not own session state
- The Web application does not own session state
- AI components do not own session state

All state transitions and mutations are performed by the Sheetwise Core and persisted at the session level.

---

## Session Scope

A session is intentionally limited in scope.

One session corresponds to:

- one data source
- one analytics model
- one output dataset

Multiple datasets must be prepared using separate sessions.

This constraint avoids ambiguity and preserves clarity.

---

## Session Identity

Each session has a unique identifier.

This identifier is used to:

- reference the session across interfaces
- generate preview and export links
- ensure consistent access to outputs

Session identifiers are opaque and must not encode business meaning.

---

## Session Ownership and Access

Each session has a creator.

Access rules are intentionally simple:

- the creator may interact with the session
- additional access may be granted explicitly
- interfaces must enforce access through the Core

Authentication and authorization mechanisms are outside the scope of this document.

---

## Session State

A session progresses through a defined lifecycle.

At a high level, sessions may be:

- **draft** — analytics intent is being defined
- **validated** — the analytics model is complete and valid
- **ready** — output has been generated

Detailed lifecycle rules are defined in a separate lifecycle specification.

---

## Session Mutability

Sessions are mutable until finalized.

- Analytics intent may evolve during the session
- The analytics model may be revised
- Validation may be re-run

Once output is generated, the session state must reflect this explicitly.

Silent or implicit mutations are not allowed.

---

## Relationship to Interfaces

Sessions are interface-agnostic.

A session may be:

- created via Slack
- reviewed via the Web
- modified through chat-based interaction

Interfaces act as views and controllers, not as owners of session state.

---

## Relationship to Analytics Model

Each session contains exactly one analytics model.

The session ensures that:

- the model is explicit
- the model is validated before execution
- the model is the sole driver of transformation

If the analytics model is unclear, the session must not proceed to execution.

---

## Summary

The session model is the backbone of Sheetwise.

By treating sessions as first-class objects, Sheetwise achieves consistency, reproducibility, and interface independence.

If a concept does not fit within a session, it does not belong in Sheetwise.
