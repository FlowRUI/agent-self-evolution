# Architecture and Trust Boundaries

The architecture separates runtime evidence, semantic reasoning, and deterministic governance. This prevents a probabilistic component from acquiring unchecked write authority.

## Component model

```mermaid
flowchart TB
  subgraph Evidence[Evidence Plane]
    I[Sanitized event index] --> W[Bounded evidence window]
    W --> P[Provenance references]
  end
  subgraph Reasoning[Reasoning Plane]
    A[Attribution] --> K[Lesson extraction]
    K --> O[Owner routing]
  end
  subgraph Control[Control Plane]
    S[Schema validator]
    D[Duplicate detector]
    R[Privacy rules]
    H[Human review]
  end
  P --> A
  O --> S
  O --> D
  O --> R
  S --> H
  D --> H
  R --> H
  H --> V[Versioned asset]
```

## Trust matrix

| Component | Trusted for | Not trusted for |
| --- | --- | --- |
| Raw runtime trace | Local investigation | Direct publication or durable memory |
| Sanitized evidence | Supporting a bounded claim | Proving causality by itself |
| Model diagnosis | Generating hypotheses | Final admission or write authority |
| Deterministic validator | Contract and policy checks | Semantic usefulness |
| Human reviewer | Adoption decision | Replacing regression evidence |

## Failure containment

```mermaid
sequenceDiagram
  participant R as Runtime
  participant E as Evidence layer
  participant L as Learning model
  participant G as Deterministic gates
  participant H as Reviewer
  R->>E: append sanitized event references
  E->>L: bounded evidence window
  L->>G: candidate + citations + owner
  alt contract fails
    G-->>L: one repair receipt
    L->>G: corrected candidate
  end
  G->>H: validated proposal
  H-->>R: adopt only after approval
```

## Architectural invariants

1. Every semantic claim has provenance.
2. Every candidate receives exactly one decision.
3. Every accepted edit has one existing owner.
4. No probabilistic component can publish directly.
5. Validation receipts are derived from final proposed text.
6. Private context is unnecessary for understanding a public rule.

## Data minimization

The public model needs event types, causal relationships, and validation outcomes—not identities, payloads, repository names, endpoints, or operational thresholds. Sanitization happens before learning, not after a proposal is written.
