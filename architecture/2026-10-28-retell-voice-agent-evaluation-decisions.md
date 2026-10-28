# Architecture decisions: enterprise voice-agent benchmark

**Date:** 2026-10-28  
**Status:** Proposed for lab evaluation; not a description of Retell's proprietary production system.

## ADR-01 — Separate streaming orchestration from model selection
**Context:** Voice performance depends on turn-taking, streaming and model output.  
**Decision:** Use a dedicated orchestrator and replaceable model adapters.  
**Alternatives:** One monolithic prompt+model workflow; vendor-only black box.  
**Consequences:** Better comparability and instrumentation; extra engineering complexity.  
**Validation:** Swap models with the same tool schemas and dataset.

## ADR-02 — Measure caller-perceived latency and model latency separately
**Context:** First-sentence latency differs from time-to-first-audio.  
**Decision:** Capture timestamped audio ingress, turn end, model request, first token, first sentence, and first audio. Report P50/P95 with repeated trials.  
**Consequences:** More defensible comparisons; requires synchronized clocks and audio instrumentation.  
**Validation:** Known-delay synthetic fixtures and repeated runs.

## ADR-03 — Keep authorization outside model output
**Context:** Models can select incorrect tools or request consequential actions.  
**Decision:** Enforce allowlists, least privilege, read-only defaults and human approval in a policy gateway.  
**Consequences:** Slower high-risk flows; auditable trust boundary.  
**Validation:** Deny/allow matrix including malformed arguments and prompt-injection attempts using harmless synthetic text.

## ADR-04 — Use synthetic data and minimize retention
**Context:** Call audio, transcripts and CRM records can contain sensitive data.  
**Decision:** Synthetic calls only in demo; redact logs, apply short retention, avoid public recordings of real customers, restrict access.  
**Consequences:** Lower privacy risk; synthetic data may miss real-world accents and noise.  
**Validation:** Inspect logs and artifacts for PII; run noise and accent diversity tests.

## ADR-05 — Optimize cost per successful resolution
**Context:** Cheap tokens do not necessarily imply cheaper completed tasks.  
**Decision:** Record inference, speech, platform and retry costs; divide by verified successful resolutions.  
**Consequences:** More accurate economics; must define success and escalation consistently.  
**Validation:** Compare measured costs under equivalent scenarios and load.

## ADR-06 — Treat vendor benchmarks as hypotheses
**Context:** Retell's vCX benchmark is internal and workload-specific.  
**Decision:** Label vendor-reported figures; run independent scenarios with confidence intervals; publish harness configuration and limitations.  
**Consequences:** Reduces sensationalism; results may differ from vendor figures.  
**Validation:** Re-run benchmark on another day and region; disclose deviations.
