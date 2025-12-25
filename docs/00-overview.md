# Overview

Sheetwise is an analytics readiness layer.

It exists to bridge the gap between messy operational data and meaningful analysis performed in BI tools.

Many organizations already have data, but struggle to use it consistently for analysis. The problem is rarely the absence of data, but the absence of a clear, trusted data shape.

Sheetwise focuses on preparing data into a form that is stable, explicit, and ready to be consumed by humans and BI tools alike.

---

## What Sheetwise Is

Sheetwise is a system that helps users:

- define _what_ they want to analyze
- make analytics intent explicit
- produce flat, deterministic datasets

The output of Sheetwise is not insight,
but **clarity in data structure**.

---

## What Sheetwise Is Not

Sheetwise is not:

- a BI or visualization tool
- a data warehouse
- an ETL orchestration platform
- an automated insight or decision engine

Sheetwise intentionally stops
before interpretation and decision-making begin.

---

## Core Idea

Sheetwise separates three concerns:

1. **Intent** — what users want to analyze
2. **Execution** — how data is transformed deterministically
3. **Interpretation** — how results are understood and acted upon

Sheetwise is responsible only for the first two.

---

## Interaction Model

Users interact with Sheetwise primarily through chat-based interfaces (such as Slack or web chat) to describe their analytics intent.

The resulting datasets are reviewed and trusted through a web-based interface before being exported to BI tools.

---

## Status of This Repository

This repository currently contains:

- product design documents
- system architecture specifications
- analytics model definitions

Implementation details are intentionally excludeduntil the core concepts are fully validated.
