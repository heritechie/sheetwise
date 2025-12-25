# Analytics Model Specification

This document defines the analytics model used by Sheetwise.

The analytics model is the core abstraction that connects user intent, data transformation, and BI-ready output.

If the analytics model is incorrect or ambiguous, the resulting dataset must be considered invalid.

---

## Purpose of the Analytics Model

The analytics model exists to make analytics intent explicit.

It answers three fundamental questions:

1. **At what granularity is time represented?**
2. **What is being measured?**
3. **Along which dimensions is the measurement grouped?**

Sheetwise does not allow data preparation without a defined analytics model.

---

## Core Components

An analytics model consists of the following components:

- Time
- Metrics
- Dimensions
- Validation State

All components are mandatory unless explicitly stated otherwise.

---

## Time Component

The time component defines how records are aggregated over time.

### Definition

A time component consists of:

- a source field
- a fixed time grain

```json
{
  "time": {
    "field": "created_at",
    "grain": "day"
  }
}
```

### Supported Time Grains

- day
- week
- month

Time grains must be calendar-based and deterministic.

### Rules

- The time field must represent a point in time
- The time grain must be explicitly defined
- Implicit time grouping is not allowed

If a dataset has no valid time field, it cannot be considered BI-ready.

---

### Metrics

Metrics define what is being measured.

### Definition

A metric consists of:

- an aggregation function
- a target field (optional, depending on function)
- a stable metric name

```json
{
  "metrics": [
    {
      "name": "rejected_users",
      "function": "count"
    }
  ]
}
```

### Supported Metric Functions

- count
- sum
- avg
- min
- max

### Rules

- Metrics must be explicitly declared
- Metric names must be stable and human-readable
- Derived or inferred metrics are not allowed

Metrics must be reproducible given the same input data.

---

### Dimensions

Dimensions define how metrics are grouped.

### Definition

A dimension is a categorical field used for grouping.

### Rules

- Dimensions must be explicitly listed
- High-cardinality dimensions must trigger warnings
- Nested dimensions must be flattened before use

Dimensions control how rows are produced in the final dataset.

---

### Model Completeness

An analytics model is considered complete when:

- A time component is defined
- At least one metric is defined
- Zero or more dimensions are defined
- All components pass validation

A model that is incomplete must not produce output.

---

### Validation Rules

Before execution, an analytics model must be validated.

### Validation Checks

- Time field exists and is valid
- Time grain is supported
- Metric functions are supported
- Dimension fields exist
- Metric–dimension combinations are deterministic

### Validation Outcomes

- valid: model can be executed
- valid_with_warnings: execution allowed, but caution advised
- invalid: execution must be blocked

Warnings do not block execution, but must be visible to the user.

---

### Execution Semantics

The analytics model defines deterministic execution semantics.

Conceptually, execution follows this order:

1. Normalize and flatten input records
2. Apply time grouping
3. Group by dimensions
4. Apply metric aggregations
5. Produce a flat tabular output

The analytics model does not define:

- storage format
- indexing
- performance optimizations

---

### Example: Valid Analytics Model

```json
{
  "time": {
    "field": "created_at",
    "grain": "day"
  },
  "metrics": [
    {
      "name": "rejected_users",
      "function": "count"
    }
  ],
  "dimensions": ["rejection_reason"]
}
```

This model produces a dataset suitable for BI tools, with a clear time axis, explicit metrics, and stable dimensions.

---

### Example: Invalid Analytics Model

```json
{
  "metrics": [
    {
      "name": "users",
      "function": "count"
    }
  ]
}
```

This model is invalid because:

- No time component is defined
- The resulting dataset cannot be meaningfully analyzed over time

---

### Relationship to AI

AI may assist in suggesting analytics models, but the final model must always be explicit and reviewable.

AI-generated models are treated as suggestions, not authoritative definitions.

### Design Constraints

The analytics model is intentionally limited.

It does not support:

- joins across multiple data sources
- nested aggregations
- window functions
- real-time streaming semantics

These constraints exist to preserve clarity and determinism.

### Summary

The analytics model is the backbone of Sheetwise.

It transforms vague analytical intent into a precise, executable, and reviewable definition.

If the analytics model is unclear, the dataset is not ready.
