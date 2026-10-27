# Architecture decisions — AI-assisted security review pilot

**Date:** 2026-10-27  
**Status:** Proposed, for review  
**Related article:** [OpenAI and CodeRabbit's $1M Security Scan Initiative](../articles/2026/10/2026-10-27-openai-coderabbit-daybreak-free-security-scans.md)  
**Diagram:** [Mermaid source](2026-10-27-coderabbit-daybreak-security-pipeline.mmd)

> This is a vendor-neutral *reference evaluation architecture*. It is not a claim about CodeRabbit's internal system implementation.

## ADR-001 — Independent review of AI-authored changes

**Context:** A coding agent may systematically reproduce its own assumptions or errors when reviewing its output.

**Decision:** Keep AI security review separate from code generation, with conventional scanners and independent human validation.

**Alternatives:** Trust the coding agent's own review; use only SAST; use AI scan as a release gate.

**Consequences:** More tools and triage work, but less single-agent correlated failure. Measure false positives and incremental confirmed detections.

## ADR-002 — Verification only in isolated disposable environments

**Context:** A security finding may require runtime confirmation. Production reproduction is unsafe.

**Decision:** Only permit synthetic fixtures and explicitly authorized test targets in network-restricted, short-lived sandboxes. No production credentials, customer data, or unrestricted egress.

**Alternatives:** Verification on staging with production data; no verification.

**Consequences:** Better containment and reproducibility; some production-only vulnerabilities may remain unverified. Log all tests and limitations.

## ADR-003 — Humans control merge and deployment

**Context:** Automatically generated fixes can introduce regressions or new security weaknesses.

**Decision:** Require code-owner approval, automated tests, and protected branch policy before merging.

**Alternatives:** Auto-merge high-confidence fixes; rely on model confidence.

**Consequences:** Slower but accountable delivery. Track time-to-remediation and accepted fix rate.

## ADR-004 — Minimal repository permissions and vendor risk review

**Context:** Whole-repo analysis exposes intellectual property and potentially sensitive code.

**Decision:** Begin with a synthetic public test repository. For private repositories, obtain AppSec/legal approval, inspect data-handling terms, scope repository grants, and rotate/remove credentials found in history before access.

**Alternatives:** Connect all organization repositories immediately.

**Consequences:** Slower onboarding but smaller blast radius and clearer data governance.

## ADR-005 — Findings are evidence, not truth

**Context:** AI-generated security findings can be plausible but false; vendor-reported totals are not independent benchmarks.

**Decision:** Maintain a triage state machine: new → evidence collected → validated / not reproducible / accepted risk → remediation / closure. Use seeded ground truth for precision/recall comparisons.

**Alternatives:** Treat all AI findings as confirmed vulnerabilities.

**Consequences:** Better reporting quality and auditable security decisions at the cost of triage time.

## ADR-006 — Promotional economics must not define long-term architecture

**Context:** The October 2026 offer is limited to a $1M pool, up to $5K per eligible organization, through November 8 while allocation lasts.

**Decision:** Evaluate both promotional and post-offer costs. Keep baseline CI security tooling operational and avoid locking the delivery pipeline to a time-limited trial.

**Alternatives:** Replace existing scanners during the promotion.

**Consequences:** More conservative savings estimates but a viable post-pilot operating model.

## Validation and exit criteria

- Synthetic known-issue test set is documented and reproducible.
- Zero verification traffic reaches production systems.
- Findings have traceable evidence and dispositions.
- Every remediation PR passes regression tests and human review.
- Scan time, confirmed detections, false positives, reviewer time, and projected steady-state costs are recorded.
- Claims about OpenAI Daybreak GPT cyber model integration are labeled **planned** until verified live.
