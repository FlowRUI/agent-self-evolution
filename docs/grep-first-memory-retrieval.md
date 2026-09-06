# Grep-First Memory Evidence Retrieval

> A privacy-aware pattern for finding grounded evidence in long agent histories before proposing reusable knowledge.

This document is an independently rewritten, implementation-free design note. All examples are synthetic. It does not reproduce an employer's code, prompts, logs, directory layout, service names, schemas, or operating parameters.

## Problem

Long-running agents generate far more history than can safely or efficiently fit into one model context. A useful memory layer must answer two different questions:

1. **Where is the relevant evidence?**
2. **Is that evidence strong enough to support a reusable rule?**

Loading every past conversation is expensive and increases privacy exposure. Summaries alone are also insufficient because they can erase the exact observation that justifies a conclusion.

## Core idea

Use deterministic text search as the first retrieval stage, then let semantic reasoning operate only on small, cited evidence windows.

```mermaid
flowchart LR
    Q[Learning question] --> N[Normalize search terms]
    N --> G[Grep lightweight index]
    G --> C[Candidate record IDs]
    C --> W[Load bounded evidence windows]
    W --> R[Redact sensitive fields]
    R --> A[Attribute failure or success]
    A --> D[Deduplicate against existing guidance]
    D --> P[Reviewable knowledge proposal]
```

## Retrieval layers

### 1. Lightweight index

Store only fields needed for discovery: synthetic record ID, timestamp, tool category, outcome label, and redacted keywords. Do not place raw credentials, full tool payloads, user identifiers, or proprietary content in the searchable index.

### 2. Deterministic candidate search

Start with exact terms, error families, tool categories, and known event labels. A generic local example might look like:

```bash
rg -n -i 'timeout|precondition|retry' ./synthetic-memory-index/
```

The command is illustrative only; it does not reveal an internal path or log format.

### 3. Bounded evidence windows

Load a small number of records around each hit. Preserve stable record IDs so every later diagnosis can cite the source window without publishing the full history.

### 4. Semantic attribution

Reason over the bounded windows to distinguish symptoms from causes. The result should state:

- the observed condition;
- the visible failure;
- the evidence record IDs;
- the safe diagnostic action;
- uncertainty and counter-evidence.

### 5. Existing-knowledge search

Search current guidance for equivalent or conflicting rules before proposing a change. Similar wording is not enough: compare applicability conditions and failure boundaries.

## Why grep first?

- Predictable: exact matches are reproducible and easy to inspect.
- Cheap: most irrelevant history is never loaded into a model context.
- Privacy-aware: retrieval can operate on a redacted index.
- Grounded: candidates retain a direct path back to evidence IDs.
- Complementary: semantic retrieval can be added after deterministic narrowing.

## Admission policy

A memory-derived proposal should be rejected when it is:

- supported by only one ambiguous incident;
- specific to a private business term, field, threshold, or migration;
- already covered by existing guidance;
- stronger than the cited evidence;
- impossible to apply without access to the original private context.

Accepted proposals should enter a review queue rather than mutate reusable skills silently.

## Privacy gates

Before any evidence leaves its original environment:

1. Remove credentials, tokens, cookies, endpoints, domains, account IDs, emails, and filesystem paths.
2. Replace real users, sessions, projects, tables, metrics, and incidents with synthetic examples.
3. Omit full prompts, raw model outputs, screenshots, stack traces, and tool payloads.
4. Keep only the minimum context required to explain the engineering pattern.
5. Run a deterministic secret and identifier scan.
6. Require human approval for public release.

## Limits

Grep does not understand paraphrases and can miss evidence whose vocabulary changed. A mature system may combine lexical search, metadata filters, and semantic retrieval, while keeping deterministic citations and privacy gates as invariants.
