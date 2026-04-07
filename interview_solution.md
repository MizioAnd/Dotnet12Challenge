# .NET 12 Product Proposal: In-Box Durable Agent Runtime

*Author: Product Owner, .NET Platform — Microsoft Developer Division*
*Date: 2026-04-07*
*Target release: .NET 12 (GA target: November 2027)*

**Thesis:** Promote durable, replayable, observable AI-agent execution from an out-of-band preview NuGet package into a first-class, in-box .NET 12 platform primitive.

---

## 1. Executive Summary

Our developers building production agentic AI on .NET today must stitch together a still-preview `Microsoft.Agents.AI` package <!-- https://www.nuget.org/packages/Microsoft.Agents.AI/ -->, an experimental `Microsoft.Agents.AI.DurableTask` extension <!-- https://www.nuget.org/packages/Microsoft.Agents.AI.DurableTask/1.0.0-preview.260225.1 -->, hand-rolled checkpointing, and unstable OpenTelemetry GenAI conventions. **The single top priority for .NET 12 is to make a Durable Agent Runtime an in-box, supported, AOT-friendly, OTel-stable platform capability** — not another framework, but the .NET answer to "where do production agents run." This is our one priority. Everything else in .NET 12 takes a back seat.

## 2. The Single Top Priority

**Ship an in-box Durable Agent Runtime in .NET 12: a supported BCL/`Microsoft.Extensions.*` surface that makes long-running, crash-resilient, deterministically replayable agent workloads a first-class .NET citizen.**

Scope boundary — what this IS: a hosting and execution model, a checkpoint-store contract, a deterministic replay log, a sandboxed tool-invocation pipeline, and stable OpenTelemetry GenAI emission. What this is NOT: a new LLM SDK, a Semantic Kernel replacement, or a general-purpose workflow engine.

## 3. Why Now

### Market signal
The Microsoft Agent Framework reached its 1.0 release in early 2026 but its core durable surface (`Microsoft.Agents.AI` 1.0.0-rc4, `Microsoft.Agents.AI.DurableTask` 1.0.0-preview) is still shipping as out-of-band NuGet previews <!-- https://www.nuget.org/profiles/MicrosoftAgentFramework/ -->. Customers asking us "how do I run an agent in production on .NET" today get a preview answer. .NET 12 closes that gap.

### Ecosystem signal
`microsoft/semantic-kernel` issue **#13435 — "Deterministic execution, replay, and audit gaps for long-running Semantic Kernel agent workflows"** (filed 2026-01-02, currently open with the `triage` label) <!-- https://github.com/microsoft/semantic-kernel/issues/13435 --> documents the exact gap in user words: explicit execution boundaries, checkpoint snapshots, replay APIs that do not re-trigger side effects, and tamper-evident audit hashes for regulated environments. Community work like `AgentFlightRecorder.NET` referenced in that thread is filling the void today. This is our strongest validation.

### Strategic signal
At Build 2025 and Ignite 2025, Microsoft positioned Azure AI Foundry Agent Service as the cloud surface for agentic AI <!-- https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/foundry-agent-service-at-ignite-2025-simple-to-build-powerful-to-deploy-trusted-/4469788 --> <!-- https://azure.microsoft.com/en-us/blog/actioning-agentic-ai-5-ways-to-build-with-news-from-microsoft-ignite-2025/ -->. .NET 11 Preview 1 release notes contain no in-box agent runtime story <!-- https://devblogs.microsoft.com/dotnet/dotnet-11-preview-1/ -->. .NET 10 GA (LTS, Nov 2025) shipped runtime, library, and SDK improvements but no in-box durable-agent primitive <!-- https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview -->. For the "AI for every developer" narrative to hold on the runtime side, .NET 12 must do for durable agents what `HttpClient` did for HTTP and what `IHostedService` did for background work.

## 4. Concrete Pain Points Today

- **Mid-run crashes lose state.** A 40-minute multi-tool agent loop crashes after the third tool call. Current DIY: hand-rolled EF/Cosmos checkpointing. Insufficient: no replay semantics, no determinism guarantees, no GenAI-aware tracing.
- **Side-effect re-execution on restart.** Without classification of tool side effects, naive replay re-charges credit cards or re-sends emails. Issue #13435 explicitly calls this out.
- **No stable observability story.** OpenTelemetry GenAI semantic conventions are still pre-stable <!-- https://opentelemetry.io/docs/specs/semconv/gen-ai/ -->, leaving instrumentation libraries pinned to experimental versions.
- **Preview-only durable extension.** `Microsoft.Agents.AI.DurableTask` is a preview NuGet built on Azure Durable Task; it works, but enterprises will not ship preview packages into regulated workloads.
- **No AOT story for agent hosts.** Current durable execution paths rely on reflection-heavy serialization, blocking AOT-first deployment models that .NET 10 invested heavily in <!-- https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview -->.
- **Audit & replay gaps for regulated industries.** Finance/healthcare ISVs need tamper-evident, time-travelable execution logs. Today they build them themselves.

## 5. Proposal: In-Box Durable Agent Runtime

### 5.1 Core primitives
- `IDurableAgent`, `AgentHost`, `IAgentCheckpointStore`, `IToolInvoker` with side-effect classification
- A deterministic replay log with recorded-IO semantics
- A pluggable sandboxing/permission model for tool calls

### 5.2 Programming model (sketch)
A C# author writes a class implementing `IDurableAgent`, declares its tool surface with attributes that classify side effects (`[Idempotent]`, `[ExternalEffect]`), and registers it via `builder.Services.AddDurableAgent<MyAgent>()`. Hosting integrates with ASP.NET Core minimal hosting and generic host.

### 5.3 Runtime guarantees
At-least-once tool execution with idempotency keys, deterministic replay against a recorded IO log, crash recovery, pause/resume, and time-travel debugging surfaced through `dotnet-trace` and Visual Studio.

### 5.4 Integration points
Builds on `Microsoft.Extensions.AI` abstractions (already GA), interoperates with Semantic Kernel and the Microsoft Agent Framework <!-- https://github.com/microsoft/agent-framework -->, ships stable OpenTelemetry GenAI emitters, and provides a first-party `AgentCheckpointStore` for Azure Storage, SQL, and Cosmos.

### 5.5 Explicit non-goals
Not a new LLM client SDK. Not a Semantic Kernel replacement. Not a general workflow engine for non-agent workloads. Not a managed cloud service (that remains Azure AI Foundry Agent Service's job).

## 6. Competitive & Ecosystem Landscape

- **Python agent orchestration runtimes** — LangGraph, CrewAI, AutoGen dominate prototyping. .NET lacks an equivalent in-box runtime story; today we point developers to a preview NuGet.
- **JVM / general-purpose durable execution** — Temporal and Restate offer durable execution as a category. .NET has Durable Task Framework but no agent-aware layer above it that ships in-box.
- **Hyperscaler agent services** — AWS Bedrock Agents and Google Vertex AI Agent Builder are managed cloud surfaces. .NET needs a runtime story that runs anywhere — on-prem, edge, sovereign cloud — not only in Azure.
- **Microsoft's own surface** — Semantic Kernel, `Microsoft.Extensions.AI`, Microsoft Agent Framework, Azure AI Foundry Agent Service, Orleans, Dapr. Each is excellent in isolation; none is the in-box, supported, AOT-friendly, OTel-stable runtime primitive that Issue #13435 asks for.

## 7. Microsoft Strategy Alignment

This proposal is the runtime expression of Microsoft's stated agentic-AI direction at Build 2025 and Ignite 2025 <!-- https://azure.microsoft.com/en-us/blog/actioning-agentic-ai-5-ways-to-build-with-news-from-microsoft-ignite-2025/ -->. Azure AI Foundry Agent Service answers "where do agents live in the cloud." .NET 12 must answer "what runtime do agents run on" — the production substrate beneath Copilot scenarios, ISV agent products, and enterprise self-hosted deployments. Without an in-box answer, .NET cedes the agent-runtime category to Python and JVM stacks just as the market consolidates.

## 8. Risks & Mitigations

| Category | Risk | Mitigation |
|---|---|---|
| Technical | Deterministic replay across non-deterministic LLM calls; AOT compatibility; checkpoint-store performance | Replay-by-recorded-IO; AOT-first source generators; pluggable stores with reference benchmarks |
| Adoption | "Yet another agent framework"; SK + Orleans investments; Python-first prototyping habits | Ship as additive layer over `M.E.AI` and Microsoft Agent Framework; SK and MAF interop samples; migration guide from preview NuGet |
| Organizational | Overlap with Azure AI Foundry Agent Service team; risk of de-prioritization if Foundry ships managed equivalent | Joint design review with Foundry team before .NET 12 Preview 1; shared RFC; OSS-first development in `dotnet/extensions` |
| Ecosystem | Fragmentation with the Microsoft Agent Framework community currently on preview NuGet | Co-evolve: in-box runtime is the supported substrate, MAF remains the higher-level authoring framework |

## 9. Success Metrics

- **≥3 named production reference customers** within 6 months of GA (target May 2028)
- **≥10,000 NuGet downloads/month** of the in-box agent hosting metapackage within 3 months of GA
- **≥5 community-contributed `IAgentCheckpointStore` providers** by GA
- **≥80% of new `Microsoft.Extensions.AI` sample apps** use the in-box durable runtime by GA + 6 months
- **p95 checkpoint write latency <50 ms** on the reference Azure Storage store
- **≥2 named ISV integrations** announced at .NET Conf 2027
- **OpenTelemetry GenAI conventions reach stable status** with .NET as a reference implementation by GA

## 10. Roadmap (assumes published .NET cadence)

- **Preview 1 (target ~Feb 2027):** core primitives, in-memory checkpoint store, MAF/SK interop samples, draft AOT story
- **Mid-cycle preview:** durable stores (Azure Storage, SQL, Cosmos), stable OpenTelemetry GenAI emission, full AOT support, side-effect classification attributes
- **GA (target Nov 2027):** production hardening, ≥3 reference customers, migration guide from `Microsoft.Agents.AI.DurableTask` preview, regulated-industry audit hash support

The word "target" is intentional everywhere — cadence is assumption, not commitment.

## 11. Open Questions

- Should the checkpoint log be the source of truth, or a side-effect of an event-sourced grain?
- How do we reconcile recorded-IO replay with non-deterministic tool side effects in regulated workloads?
- Does this ship as `Microsoft.Extensions.Agents.Hosting` or as a new `Microsoft.AspNetCore.Agents.*` namespace?
- What is the exact split of responsibility between this runtime and the Microsoft Agent Framework's higher-level authoring surface?
- Do we co-own the OpenTelemetry GenAI stabilization work with the OTel SIG?

---

## 12. Appendix: Verification Log

<!--
Sources consulted during pre-write verification (2026-04-07):

Forbidden-claims check (confirmed clear — none of the below were proposed as new):
- .NET 10 GA notes: https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview
  Confirmed: no in-box durable agent runtime, no agent hosting primitives. .NET 10 ships JIT/AOT/library/SDK improvements, C# 14, EF Core 10, MAUI, ASP.NET Core 10. No agent runtime.
- .NET 11 Preview 1: https://devblogs.microsoft.com/dotnet/dotnet-11-preview-1/
  Confirmed: no AI/agent/Microsoft.Extensions.AI/durable-execution mentions in Preview 1 themes. Focus is runtime async, Zstandard, BFloat16, C# 15 collection expression args, Blazor, EF Core, RISC-V/s390x.
- Microsoft Agent Framework: https://github.com/microsoft/agent-framework
  Confirmed: distributed as out-of-band NuGet (Microsoft.Agents.AI), not part of .NET 10 or 11. Has Durable Agents support via Microsoft.Agents.AI.DurableTask preview package.

Concrete signals gathered (3, all citable):
1. Ecosystem: microsoft/semantic-kernel issue #13435 (filed 2026-01-02, open, triage label, 5 comments)
   https://github.com/microsoft/semantic-kernel/issues/13435
2. Strategic: Azure AI Foundry Agent Service GA + Ignite 2025 announcements
   https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/foundry-agent-service-at-ignite-2025-simple-to-build-powerful-to-deploy-trusted-/4469788
   https://azure.microsoft.com/en-us/blog/actioning-agentic-ai-5-ways-to-build-with-news-from-microsoft-ignite-2025/
3. Market: Microsoft.Agents.AI 1.0.0-rc4 and Microsoft.Agents.AI.DurableTask 1.0.0-preview still in preview as of 2026-04
   https://www.nuget.org/packages/Microsoft.Agents.AI/
   https://www.nuget.org/packages/Microsoft.Agents.AI.DurableTask/1.0.0-preview.260225.1
   https://www.nuget.org/profiles/MicrosoftAgentFramework/

Additional anchors:
- OpenTelemetry GenAI semantic conventions (still pre-stable as of early 2026):
  https://opentelemetry.io/docs/specs/semconv/gen-ai/

Hedged claims (verification inconclusive or directional):
- .NET Conf 2027 ISV integration count — directional, tied to GA
- Exact OTel GenAI stabilization timeline — flagged as a target outcome, not a fact
- Roadmap dates — every date carries the word "target"
- Customer counts and download counts — projected metrics, not measured
-->

End of proposal.
