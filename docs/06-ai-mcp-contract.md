# AI MCP Contract

This document defines the contract between Sheetwise and AI systems through the MCP (Model Context Protocol).

The purpose of this contract is to clearly define:

- what AI is allowed to do
- what AI is not allowed to do
- how AI interacts with the analytics model

This document exists to prevent ambiguity and scope creep in the use of AI within Sheetwise.

---

## Role of AI in Sheetwise

AI in Sheetwise serves a **supporting role**.

Its primary responsibility is to assist in translating human analytical intent into a structured analytics model.

AI is not an executor, decision-maker, or interpreter of results.

---

## What AI Is Allowed to Do

AI may:

- parse natural language intent expressed by users
- suggest analytics model components (time, metrics, dimensions)
- explain assumptions and trade-offs
- surface potential issues or ambiguities in intent
- ask clarifying questions when intent is incomplete

AI outputs are always treated as **suggestions**, not commands.

---

## What AI Is Not Allowed to Do

AI must never:

- execute data transformations
- access raw or transformed data
- validate or finalize analytics models
- generate insights, summaries, or conclusions
- modify session state directly
- bypass deterministic validation rules

Any attempt to delegate these responsibilities to AI violates the design principles of Sheetwise.

---

## MCP Input Contract

AI receives only **contextual summaries**, never raw data.

### Allowed Inputs

- user intent (natural language)
- schema summaries (field names, types, high-level structure)
- existing analytics model state (if any)
- validation warnings or errors (as text)

### Disallowed Inputs

- raw records
- sample rows containing sensitive data
- transformation outputs
- execution results

The MCP must enforce strict input filtering.

---

## MCP Output Contract

AI outputs must be **structured and machine-readable**.

Free-form or narrative-only responses are not acceptable as primary outputs.

### Required Output Shape

AI must return a structured suggestion object, for example:

```json
{
  "suggested_model": {
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
  },
  "notes": [
    "Detected event-level records with timestamps",
    "High cardinality possible on rejection_reason"
  ],
  "confidence": "medium"
}
```

### Output Rules

- All fields must be explicit
- Defaults must be visible
- Ambiguities must be called out
- Confidence must not imply correctness

### Model Acceptance

AI-suggested models are never accepted automatically.

The Sheetwise Core is responsible for:

- validating the model
- enforcing completeness rules
- rejecting invalid suggestions
- requesting clarification if needed

Only validated models may proceed to execution.

### Relationship to Sessions

AI does not own or manage sessions.

- AI may read session context
- AI may suggest updates to the analytics model
- AI may not mutate session state

All session mutations must go through the Core.

### Error Handling and Ambiguity

When intent is unclear, AI must prefer clarification over assumption.

Examples:

- unclear time granularity
- ambiguous metric definitions
- missing grouping dimensions

In such cases, AI should ask explicit questions rather than guessing.

### Determinism and Repeatability

AI suggestions must not introduce nondeterministic behavior.

Given the same inputs, AI may produce different suggestions, but execution outcomes must always be governed by deterministic rules in the Core.

AI variability must never affect execution semantics.

### Security and Privacy Considerations

AI must be treated as an untrusted external component.

- Inputs must be minimized
- Outputs must be validated
- Sensitive data must never be exposed

The MCP boundary exists to enforce these constraints.

### Anti-Patterns

The following patterns are explicitly disallowed:

- "Let the AI decide the best metric"
- "Auto-run transformations suggested by AI"
- "Generate insights from the dataset"
- "Summarize trends or anomalies"

If a feature requires these behaviors, it does not belong in Sheetwise.

### Summary

AI in Sheetwise exists to reduce friction, not to replace judgment.

By enforcing a strict MCP contract, Sheetwise ensures that AI enhances clarity without compromising determinism, trust, or accountability.
