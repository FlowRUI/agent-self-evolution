# Architecture

The reference pipeline deliberately keeps probabilistic and deterministic responsibilities separate.

```mermaid
flowchart TB
    subgraph Input
      T[Trace events]
    end
    subgraph Analysis
      R[Evidence retriever]
      A[Failure attribution]
      K[Knowledge extractor]
    end
    subgraph Governance
      P[Skill proposal]
      V[Deterministic validators]
      Q[Human review queue]
    end
    T --> R --> A --> K --> P --> V --> Q
```

Any future implementation should preserve event citations and keep model-backed components behind the same deterministic validation boundary. Implementation details remain private.
