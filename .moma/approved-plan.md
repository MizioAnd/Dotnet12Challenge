# Approved Plan — .NET 12 Product Proposal

Approved at iteration 3, score 10/10. Target file: `interview_solution.md`.

## Theme
A first-class **Durable Agent Hosting Model** in .NET 12: a runtime + framework primitive for building long-running, stateful, LLM-driven agent workloads with built-in durability, replayable execution, tool/permission sandboxing, and first-class observability — positioned as the .NET answer to the emerging "agent runtime" category.

---

## Pre-Write Verification Step (REQUIRED before writing)

The Implementer MUST run WebFetch/WebSearch to confirm or refute each item below before producing the final document. If a claim cannot be verified, the Implementer must either (a) remove it, (b) replace with a hedged directional statement, or (c) cite the source inline.

### Forbidden-claims list (do NOT propose — already shipped or announced for .NET 10/11)
Verify against `learn.microsoft.com`, `devblogs.microsoft.com/dotnet`, `github.com/dotnet/core` that the proposal does NOT duplicate:
- Native AOT improvements / trimming for ASP.NET Core
- `Microsoft.Extensions.AI` abstractions (IChatClient, IEmbeddingGenerator, etc.)
- Semantic Kernel agent framework features
- Existing Orleans grains / Dapr workflows / Durable Task Framework
- OpenTelemetry GenAI semantic conventions support already merged
- Anything explicitly listed in .NET 10 GA notes or .NET 11 preview themes

Verification anchors:
- .NET 10 release notes: `https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview`
- .NET 11 roadmap / preview blog: `https://devblogs.microsoft.com/dotnet/`
- `dotnet/core` themes repo: `https://github.com/dotnet/core`
- `Microsoft.Extensions.AI` repo: `https://github.com/dotnet/extensions`
- Semantic Kernel: `https://github.com/microsoft/semantic-kernel`

The Implementer must cite at least one source URL **inline** (or in an `<!-- HTML comment -->`) for every .NET 10 or .NET 11 reference made in the document.

### Concrete signals to gather (≥2, ideally 3, in "Why Now")
Locate and cite at least 2 (ideally 3) verifiable signals — each with URL, GitHub issue/discussion number, conference session title, or named published survey:
- GitHub issues/discussions in `dotnet/extensions`, `microsoft/semantic-kernel`, `dotnet/aspnetcore` requesting durable/long-running agent execution, checkpointing, or replay
- .NET Conf 2025 sessions on AI agents, SK, or `M.E.AI`
- Build 2025 announcements on agentic AI in the Microsoft stack
- Published surveys (Stack Overflow Developer Survey 2025, JetBrains State of Developer Ecosystem 2025, RedMonk rankings)
- Azure product announcements for AI Foundry / Azure AI Agent Service
- LangGraph, CrewAI, AutoGen, Temporal release notes/blogs on durable-agent investment

If <2 signals can be verified, ESCALATE rather than fabricate.

---

## Document Structure (13 sections)

### 1. Header
- Title: **".NET 12 Product Proposal: Durable Agent Hosting Model"**
- Author line: *Product Owner, .NET Platform — Microsoft Developer Division*
- Date: **2026-04-07**
- Target release: **.NET 12 (GA target: November 2027)**
- One-line thesis (≤25 words) — single top priority, no hedging on the priority itself

### 2. Executive Summary (≤150 words)
State the ONE priority. Name the user (developers building production agentic AI on .NET). Name the outcome (durable, replayable, observable agent workloads as a built-in platform capability). Explicitly say "this is the single top priority for .NET 12." No secondary priorities.

### 3. The Single Top Priority
A bolded standalone sentence naming the priority. Then 2–3 sentences clarifying scope boundaries — what it is and is NOT — so reviewers can see no overlap with shipped work.

### 4. Why Now (must contain 2–3 cited concrete signals)
Three subsections:
- **Market signal** — ≥1 verifiable external data point with inline URL/footnote
- **Ecosystem signal** — ≥1 GitHub issue/discussion number from `dotnet/*` or `microsoft/semantic-kernel` showing demand for durable agents/checkpointing/replay, with inline URL
- **Strategic signal** — ≥1 Microsoft strategic anchor (Azure AI Foundry, Azure AI Agent Service, Build 2025 keynote, .NET Conf 2025) with inline URL

Where exact specifics cannot be verified, use directional language ("as of early 2026, public materials indicate…") AND still cite the source consulted.

### 5. Concrete Pain Points
Bullet list of 4–6 pain points developers hit TODAY. Each must:
- Name a concrete scenario (e.g., "an agent loop crashes after 40 minutes mid-tool-call and loses all intermediate reasoning state")
- Name the current DIY workaround (Orleans/Dapr/EF/custom checkpointing)
- Name why the workaround is insufficient (cost, complexity, no replay, weak observability)

≥1 pain point should reference a real GitHub issue if one was found in verification.

### 6. Proposal: Durable Agent Hosting Model
- **6.1 Core primitives** — `IDurableAgent`, `AgentHost`, `AgentCheckpointStore`, `IToolInvoker` with sandboxing, deterministic replay log
- **6.2 Programming model** — sketch (not full code) of authoring a durable agent in C#
- **6.3 Runtime guarantees** — at-least-once tool execution, deterministic replay, crash recovery, pause/resume, time-travel debugging
- **6.4 Integration points** — builds on `Microsoft.Extensions.AI` (cite), interoperates with Semantic Kernel (cite), pluggable into ASP.NET Core minimal hosting, OpenTelemetry GenAI conventions
- **6.5 Explicit non-goals** — not a new LLM SDK, not an SK replacement, not a workflow engine for non-agent workloads

### 7. Competitive & Ecosystem Landscape (4 explicitly-named spaces)
- **Python agent orchestration runtimes** — name LangGraph, CrewAI, AutoGen. Hedge specifics; cite repo URLs.
- **JVM / general-purpose durable execution** — name Temporal and Restate. Cite repo/docs URLs.
- **Hyperscaler agent services** — name AWS Bedrock Agents and Google Vertex AI Agent Builder. Hedge feature parity; cite product page URLs.
- **Microsoft's own surface** — name Semantic Kernel, `Microsoft.Extensions.AI`, Azure AI Agent Service, Orleans/Dapr. Cite each.

For each space: one sentence on what .NET lacks today that this proposal addresses.

### 8. Microsoft Strategy Alignment
Tie to publicly stated Microsoft themes (cite sources): Copilot platform, Azure AI Foundry, "AI for every developer," .NET as the production runtime for enterprise AI. One paragraph; no fabricated quotes.

### 9. Risks & Mitigations (≥3 distinct categories)
At least one risk in each:
- **Technical** — deterministic replay across non-deterministic LLM calls; checkpoint-store performance; AOT compatibility; debugging replayed state. *Mitigation*: replay-by-recorded-IO model, pluggable stores, AOT-first design.
- **Adoption** — developers invested in SK + custom Orleans; "yet another agent framework" perception; Python remains default for prototyping. *Mitigation*: ship as additive layer over `M.E.AI`, SK interop sample, migration guide.
- **Organizational/strategic** — overlap with Azure AI Agent Service team; unclear ownership; risk of being deprioritized if Azure ships managed equivalent. *Mitigation*: joint design review with Azure AI Foundry team before .NET 12 Preview 1, shared RFC, OSS-first development in `dotnet/extensions`.

Optional 4th: **ecosystem risk** — fragmentation with SK community.

### 10. Success Metrics (≥5, measurable, time-bound)
Each with a number and a deadline tied to the .NET 12 cycle (Preview 1 → GA Nov 2027 → +6 months post-GA). Examples:
- ≥3 production reference customers using the durable agent runtime within 6 months of GA
- ≥10,000 NuGet downloads/month of `Microsoft.Extensions.Agents.Hosting` within 3 months of GA
- ≥5 community-contributed `AgentCheckpointStore` providers by GA
- ≥80% of new `M.E.AI` sample apps using the durable hosting model by GA + 6 months
- p95 checkpoint write latency <50ms on the reference store
- ≥2 named ISV integrations announced at .NET Conf 2027

### 11. Roadmap (high level, no fabricated specifics)
Three phases aligned to public .NET cadence:
- **Preview 1 (target ~Feb 2027)** — core primitives, in-memory checkpoint store, SK interop sample
- **Mid-cycle preview** — durable stores (Azure Storage, SQL, Cosmos), OpenTelemetry GenAI integration, AOT support
- **GA (target Nov 2027)** — production hardening, reference customers, migration guides

Use the word "target" everywhere. Flag the cadence as assumption, not fact.

### 12. Open Questions
3–5 honest open questions, e.g.:
- "Should the checkpoint log be the source of truth or a side-effect of an event-sourced grain?"
- "How do we handle non-deterministic tool side effects on replay?"
- "Is this a `Microsoft.Extensions.*` package or a new `Microsoft.AspNetCore.Agents.*` namespace?"

### 13. Appendix: Verification Log
Short bulleted list (or HTML comment block) recording:
- Sources consulted during verification
- Forbidden-claims items checked and confirmed clear
- Concrete signals found and cited (with URLs)
- Any claim hedged because verification was inconclusive

The Verification Log is EXCLUDED from the 900–1400 word budget for the main document.

---

## Voice & Style Rules
- Executive product-owner tone: confident on the priority, hedged on unverifiable specifics
- First person plural ("we", "our developers") — Microsoft DevDiv PO voice
- No marketing fluff, no emoji, no fabricated quotes, no invented customer names
- Every .NET 10 / .NET 11 factual reference must have an inline citation or HTML-comment source
- Where a competitor specific cannot be verified, use directional language AND still name the competitor space explicitly
- Document length target: 900–1,400 words (excluding appendix and citations)
- Markdown only; H2/H3 headings, bullet lists, at most one table (risks or metrics)

## Implementer Checklist
1. Run the Pre-Write Verification Step. Record findings.
2. Gather ≥2 (ideally 3) concrete signals with citable URLs/IDs.
3. Confirm no forbidden-claims overlap; cite sources for every .NET 10/11 reference.
4. Write `interview_solution.md` following the 13-section structure.
5. Ensure: single top priority unambiguous, all 4 competitor spaces named, all 3 risk categories covered.
6. Append the Verification Log (Section 13).
7. Re-read end-to-end against the 10 review criteria before finishing.
