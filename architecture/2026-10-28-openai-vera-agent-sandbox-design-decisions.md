# Architecture Decisions — OpenAI Vera CPU / OCI Agent Sandbox Reference

**Date:** 2026-10-28  
**Status:** Proposed for a reproducible educational lab, not a description of OpenAI production infrastructure.  
**Related article:** [Research and YouTube brief](../articles/2026/10/2026-10-28-openai-vera-cpu-agent-sandbox.md)  
**Diagram:** [Mermaid reference architecture](./2026-10-28-openai-vera-agent-sandbox-reference.mmd)

## ADR-01 — Separate model inference and tool execution

**Context:** Agent tasks alternate between GPU-heavy model inference and CPU-heavy tool execution, parsing, retrieval, and verification.

**Decision:** Place model inference and sandbox workers in independently scalable pools; maintain an explicit request trace across both.

**Alternatives:** Single GPU-equipped node pool; remote-only model API with no observability; fully colocated tool execution.

**Consequences:** Better resource attribution and flexible scaling, at the cost of network hops and operational complexity. Some short workflows may perform better when colocated.

**Validation:** Compare p50/p95 end-to-end latency, queue time, GPU utilization, and total task cost at fixed throughput and concurrency.

## ADR-02 — Benchmark available platforms before vendor-specific hardware

**Context:** Vera on OCI is newly announced but regional access, quotas, and instance costs vary.

**Decision:** Start with an x86 baseline and optional Arm64 comparison. Add Vera as a third test condition only after verified provisioning.

**Alternatives:** Publish NVIDIA's 80% claim as if it were our measured result; use unrelated synthetic CPU benchmarks alone.

**Consequences:** Less sensational initial demo, but reproducible evidence and no false performance attribution.

**Validation:** Record VM shapes, architecture, kernel, image digest, workload commit, concurrency, price, and repeatable results. Report uncertainty and cold/warm conditions.

## ADR-03 — Enforce agent tool permissions outside the model

**Context:** Generated tool requests are untrusted inputs; a model must not approve its own actions.

**Decision:** Use an independent policy decision point, narrowly scoped tool allowlists, and human approval for consequential operations.

**Alternatives:** Prompt-only permission controls; all tools available to every agent.

**Consequences:** Some legitimate actions require approval, but scope is explicit and auditable.

**Validation:** Negative tests for unauthorized tool names, arguments, external destinations, and sensitive actions; log denied requests without leaking secrets.

## ADR-04 — Use disposable sandbox workers and deny egress by default

**Context:** AI-generated code and untrusted inputs can expose hosts, credentials, or external networks.

**Decision:** Use per-task disposable execution environments with constrained identity, CPU/memory limits, minimal filesystem access, no privileged host mounts, and default-deny outbound connectivity. Choose a stronger isolation technology for workloads whose threat model demands it.

**Alternatives:** Shared long-lived container with broad host access; trust all generated code.

**Consequences:** Cold-start overhead and additional resource consumption. Kubernetes namespaces and ordinary containers alone are not a high-assurance VM boundary.

**Validation:** Verify teardown, egress denials, quota enforcement, credential expiration, and isolation policy via harmless tests. Do not claim the lab reproduces OpenAI's containment implementation.

## ADR-05 — Measure cost per successful task rather than token price or CPU speed alone

**Context:** Faster hardware does not necessarily reduce cost when tasks are dominated by inference, network latency, or retries.

**Decision:** Collect token charges, CPU/VM-hour charges, retries, queue time, completed tasks, and error rates.

**Alternatives:** Optimize solely for tokens/second, CPU benchmark throughput, or a vendor's headline speedup.

**Consequences:** More comprehensive instrumentation but better business decisions.

**Validation:** Compute cost per verified completion at target service-level objectives; compare distributions and sensitivity to concurrency.

## ADR-06 — Distinguish published vendor facts, extrapolations, and lab results

**Context:** Oracle's October 27 launch and OpenAI's production-intent quote are verified primary-source statements; independent workload results are not yet available.

**Decision:** Label sources as (a) official announcement, (b) vendor-reported benchmark, (c) proposed architecture, or (d) measured lab result. Do not turn (b) into (d).

**Alternatives:** Treat press-release benchmarks as independent proof.

**Consequences:** Better credibility; slower to make absolute performance claims.

**Validation:** Verify every numeric figure against a dated source, record region/availability constraints, and prohibit any unmeasured percentage from appearing as a personal benchmark.

## Implementation backlog

| Phase | Work | Exit criterion |
|---|---|---|
| 1 | Synthetic task corpus, fixed random seeds, mock model | Reproducible completion counts |
| 2 | FastAPI orchestrator, queue, worker, telemetry | Full trace across each agent loop |
| 3 | Disposable sandbox and policy gateway | Negative tests pass, audit complete |
| 4 | Kubernetes separate node pools and quotas | Independent CPU-worker scaling demonstrated |
| 5 | Optional OCI Vera trial | Region, quota, Arm64 compatibility, price verified |
| 6 | Editorial release | All numbers attributed, charts reproducible, no unverified vendor claims |

## Sources

- Oracle OCI Vera GA (October 27, 2026): https://blogs.oracle.com/cloud-infrastructure/nvidia-vera-cpus-oci
- NVIDIA/Oracle release (October 27, 2026): https://nvidianews.nvidia.com/news/nvidia-and-oracle-expand-nvidia-vera-rubin-to-multi-gigawatt-scale-and-bring-nvidia-vera-cpu-to-oracle-cloud-infrastructure
