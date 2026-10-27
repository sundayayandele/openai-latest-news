# Architecture decisions: AI-generated mathematics and Lean verification

**Date:** 2026-10-27  
**Status:** Proposed reference design (not a description of OpenAI's internal infrastructure)

## ADR-001: Separate discovery from verification

**Context:** AI systems can generate plausible but incorrect reasoning.  
**Decision:** Treat manuscripts and formal proofs as candidate artifacts, not authoritative conclusions. Require a separate verification stage.  
**Alternatives:** Auto-publish after model self-check; rely on a single reviewer.  
**Consequences:** More review latency but better evidentiary discipline.

## ADR-002: Pin the formal-verification toolchain

**Context:** Lean libraries and compiler versions can change proof acceptance or reproducibility.  
**Decision:** Capture exact Lean version, dependencies, build command, artifact commit, and environment.  
**Alternatives:** Floating latest dependencies.  
**Consequences:** Reproducible checks and clearer provenance; maintenance cost.

## ADR-003: Audit the theorem statement and axioms

**Context:** A proof checker validates the proposition encoded, not necessarily the proposition claimed in prose.  
**Decision:** Review the precise theorem, computational model, encoding assumptions, admitted axioms, and trust boundary.  
**Alternatives:** Rely on a successful build indicator.  
**Consequences:** Requires specialist time; prevents specification mismatches.

## ADR-004: Independent expert acceptance gate

**Context:** Novel results may have far-reaching implications and require domain knowledge.  
**Decision:** Independent reviewers assess whether the formal result establishes the stated mathematical claim, before any strong public claim of acceptance.  
**Alternatives:** Internal sign-off only.  
**Consequences:** Slower release; more credible interpretation.

## ADR-005: Make corrections first-class

**Context:** Research claims evolve under scrutiny.  
**Decision:** Version manuscripts, verification logs, caveats, and correction notes. Preserve prior versions and distinguish verified from speculative implications.  
**Alternatives:** Silent updates.  
**Consequences:** Auditability and reduced misinformation risk.

## Threat model and controls

| Risk | Control |
|---|---|
| Model fabricates a mathematical argument | Formalization + independent expert review |
| Proof establishes weaker statement | Claim-to-theorem mapping and scope audit |
| Unreproducible artifact | Pinned toolchain and build log |
| Unexamined axioms | Axiom audit and checker provenance |
| Misleading headline | Qualified editorial language and explicit limitations |
| Dependency compromise | Minimal build environment, locked dependencies, review |

**Validation plan:** Reproduce a small Lean example; inspect OpenAI's public scope document; obtain independent verification evidence before changing the article's provisional status.

**Source:** https://openai.com/index/building-scientific-understanding-with-academic-communities/
