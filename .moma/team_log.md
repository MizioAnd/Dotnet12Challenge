# Team Log — .NET 12 Product Proposal

Final outcome: approved at iteration 3 with score 10/10. Implementer wrote `interview_solution.md`.

---

## Planner — Iteration 1

# Improvement Plan — Iteration 1

## Changes to interview_solution.md (create new file)

Create the file from scratch with the following exact structure and content. Use markdown headings, keep tone executive-but-substantive (target ~900–1100 words), and avoid bullet sprawl.

### Section 1: Title and metadata block
- Top of file: `# .NET 12 Product Proposal: A Unified, Production-Grade Runtime for Long-Running Agentic Workloads`
- Sub-line (italic): `*Author: Product Owner, .NET Platform | Target Release: November 2027 | Status: Draft for LT Review*`

### Section 2: `## 1. Executive Summary` (≈120 words)
- State the single top priority in one sentence: **"Make .NET 12 the best runtime in the industry for hosting long-running, stateful AI agents — by delivering a first-class Durable Agent Hosting Model built into ASP.NET Core and the Microsoft Agent Framework."**
- Frame: .NET 10 introduced the Microsoft Agent Framework and AI primitives; .NET 11 is hardening async via Runtime Async and adding OpenTelemetry. The natural next step in 12 is operational maturity for agents in production — durability, recoverability, cost control.
- Single priority; everything else out of scope.

### Section 3: `## 2. Vision` (≈110 words)
- 3-year arc: ".NET should be the default platform for AI agents that survive process restarts, scale to thousands of concurrent sessions, and remain debuggable in production."
- Contrast current state: today teams stitch Agent Framework + Durable Functions + Service Bus + custom checkpointing. We collapse that into a first-party hosted model.

### Section 4: `## 3. The Top Priority: Durable Agent Hosting Model` (≈220 words)
- `Microsoft.Extensions.Agents.Hosting` package providing `DurableAgentHost` integrating with `IHostedService`
- Pluggable state stores (Cosmos, SQL, Redis, in-memory) via `IAgentStateStore`
- Automatic checkpointing of conversation state, tool-call history, pending tool invocations — built on .NET 11 Runtime Async
- Resumable tool invocations after host crash
- Per-agent isolation and quotas (token, wall-clock, cost), surfaced via OpenTelemetry
- VS + Aspire dashboards showing live sessions, checkpoint history, replay
- Hosting/durability infra, NOT new model integrations or primitives

### Section 5: `## 4. Why Now — Rationale` (≈180 words)
1. Agent Framework is shipped but unproductionized — top question on dotnet/extensions issues since Nov 2025.
2. Runtime Async lands in .NET 11 → cheap suspension/resumption is the missing primitive.
3. Competitive pressure: LangGraph shipped durable execution late 2025; Temporal added LLM agent primitives early 2026.

### Section 6: `## 5. Strategic Alignment` (≈110 words)
- Azure: Cosmos, SQL, Container Apps gain first-party agent hosting story
- M365/Copilot: same model underpins Copilot extensibility
- Developer productivity: collapses 4–5 NuGet packages into a single `AddDurableAgentHost()` call
- AI strategy: positions .NET as the production runtime half complementing Python research

### Section 7: `## 6. Pain Points` (≈100 words)
- "My agent loses its conversation when the pod restarts."
- "I can't replay a failed tool call without re-running the whole agent loop."
- "I can't cap how much an individual user session costs me."
- "My ops team can't see what an agent is doing in production."
- "I rewrote checkpointing three times and it's still wrong."

### Section 8: `## 7. Risks, Trade-offs, and Mitigations` (≈170 words)
Table: Risk | Impact | Mitigation, with 5 rows: scope creep into workflow engine, state-store lock-in, Runtime Async maturity, .NET 10 LTS adoption lag, SK overlap.

### Section 9: `## 8. Success Metrics` (≈110 words)
- 25,000 unique downloads of `Microsoft.Extensions.Agents.Hosting` in first 90 days
- 500+ Azure Container Apps revisions running `DurableAgentHost` within 6 months
- "Agent lost state" issues drop 70% YoY
- 60% of production deployments enable token/cost ceilings within 6 months
- ≥80% positive on satisfaction survey
- ≥3 community-contributed `IAgentStateStore` providers by GA + 6 months

### Section 10: `## 9. Out of Scope` (≈60 words)
- New LLM provider integrations
- New language features
- New AI primitives, embeddings, vector DB
- General-purpose workflow orchestration

### Section 11: `## 10. Ask` (≈40 words)
- Staffing: 4 engineers from ASP.NET Core, 2 from Agent Framework, 1 PM, 1 runtime Async liaison, starting Q3 2026, Preview 1 in .NET 12 Preview 3.

## Critique Responses
- None — first iteration.

---

## Reviewer — Iteration 1

# Reviewer Critique — Iteration 1

## Issues to Fix

- **Criterion 2 (Avoids already-shipped features)**: Plan makes specific factual claims about .NET 10 and .NET 11 that the Planner cannot verify — "Microsoft Agent Framework and AI primitives" in .NET 10, "Runtime Async and OpenTelemetry" in .NET 11, "embeddings APIs, or vector database support (shipped in .NET 10)." The entire proposal is built on top of claimed .NET 11 features ("Runtime Async lands in .NET 11, making this technically feasible") — but .NET 11 has not yet shipped (target Nov 2026). Anchoring the core technical rationale on an unshipped release's unconfirmed feature is risky. Either soften with hedging and explicit citation of public roadmap posts, or pick a priority whose foundation is verifiable today.

- **Criterion 3 (Evidence-based rationale)**: Rationale cites specifics presented as facts but unverifiable — "LangGraph shipped durable execution in late 2025," "Temporal added first-class LLM agent primitives in early 2026," "#1 question on dotnet/extensions issues since Nov 2025." These fabricated-sounding datapoints weaken credibility. Replace with hedged language or drop specific dates and repo claims.

## What Passed
- Criterion 1: Clearly identifies one priority — Durable Agent Hosting Model.
- Criterion 4: Names LangGraph, Temporal, Semantic Kernel, Durable Functions; positions .NET against Python.
- Criterion 5: Section 5 explicitly ties to Azure (Cosmos, SQL, Container Apps), M365/Copilot, AI stack.
- Criterion 6: Section 7 lists five verbatim developer quotes mapped to capabilities in Section 4.
- Criterion 7: Section 8 is a 5-row table with Risk/Impact/Mitigation.
- Criterion 8: Section 9 provides six metrics with target numbers, timeframes, sources.
- Criterion 9: Tone executive-but-substantive; "Ask" section with specific staffing.
- Criterion 10: Ten numbered sections with word budgets totaling ~1100 words.

Score: 8/10

---

## Planner — Iteration 2

# Improvement Plan — Iteration 2

## Goals
1. Criterion 2 fix — ground all .NET 10/11 claims in verifiable public sources; treat .NET 10 (shipped Nov 2025 LTS) as historical, .NET 11 (preview, target Nov 2026) as roadmap-only with hedging.
2. Criterion 3 fix — remove fabricated specifics. Use hedged, directionally accurate language.

## Pre-writing verification step
Before drafting, use WebFetch/WebSearch on:
- `https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview`
- `https://devblogs.microsoft.com/dotnet/announcing-dotnet-10/`
- `https://github.com/dotnet/core/blob/main/release-notes/10.0/README.md`
- `https://github.com/dotnet/core/blob/main/roadmap.md`
- `https://devblogs.microsoft.com/dotnet/`
- `https://github.com/dotnet/runtime/issues?q=label%3AArea-Runtime+Async`
- `https://learn.microsoft.com/en-us/semantic-kernel/`
- `https://github.com/microsoft/semantic-kernel`
- `https://opentelemetry.io/docs/languages/net/`

Any unverifiable feature must be dropped or framed as "per the public roadmap, expected in .NET 11."

## Revised top priority (theme unchanged, foundation grounded)
**Top Priority for .NET 12: A first-class, built-in Durable Agent Hosting Model (`Microsoft.Extensions.Agents.Durable`)** — BCL-level abstraction for long-running, stateful, resumable AI agent workflows on top of whatever async/runtime primitives exist in .NET 11 at GA, with pluggable state stores and OTel observability.

## Document structure (11 H2 sections, 900–1100 words)
1. Title — `.NET 12 Product Proposal: Durable Agent Hosting as a First-Class .NET Workload`
2. Executive Summary (≤150 words) — name `Microsoft.Extensions.Agents.Durable`; no .NET 10/11 feature claims here.
3. Context: Where .NET Stands in April 2026 (~150 words) — only verified or hedged claims. Forbidden: Runtime Async, MS Agent Framework, OTel GA, vector DB unless verified.
4. The Top Priority (~180 words) — `IDurableAgent`, `IAgentStateStore`, tool-calling contract, OTel spans, ASP.NET Core integration. Scope: hosting/runtime model only.
5. Why Now (~140 words) — directional language only; no fabricated dates/rankings.
6. Strategic Alignment (~110 words) — Azure AI Foundry, AI, dev productivity, Copilot.
7. Developer & Customer Pain Points (~110 words) — checkpointing, vendor-specific tool-calling, observability, audit/compliance.
8. Risks, Trade-offs, and Mitigations (~130 words) — .NET 11 dependency, Durable Functions overlap, LLM fragmentation, "yet another framework."
9. Success Metrics (~90 words) — adoption %, first-party adopters, NuGet downloads, MTTR, satisfaction.
10. Out of Scope — new LLM SDK, UI designer, SK replacement, non-Azure stores.
11. The Ask (~60 words) — 1 PM, 6 engineers, Preview 1 spring 2027, GA Nov 2027.

## Writing rules
1. Run WebFetch/WebSearch first.
2. Never state .NET 11 features as shipped — always hedge.
3. No invented dates, vendor timelines, or issue rankings.
4. Single priority; sub-components OK, alternatives not.
5. Executive tone, 900–1100 words.
6. 11 H2 sections in order.
7. Write to `interview_solution.md`; overwrite if exists.
8. No code blocks except optionally one ≤10-line `IDurableAgent` sketch.
9. No mention of plan, Reviewer, or iteration process in the final doc.

---

## Reviewer — Iteration 2

# Critique — Iteration 2 Plan

## Strengths
The pre-writing verification step using WebFetch is the right move. Forbidden-claims list (Runtime Async, MS Agent Framework, OTel GA, vector DB) is a strong guardrail. Hedging discipline ("per the public roadmap," "currently in preview") is appropriate.

## Criterion-by-Criterion
1. Single top priority — Pass.
2. Avoids shipped features — Mostly addressed; recommend explicit auditable citation requirement.
3. Evidence-based rationale — Improved; "directional language" risks vagueness; require ≥2–3 concrete verifiable signals.
4. Ecosystem/competitors — Partially; should explicitly name comparison spaces.
5. Microsoft strategy alignment — Pass.
6. Concrete pain points — Pass.
7. Risks and mitigations — Improved; should require ≥3 distinct risk categories.
8. Measurable success metrics — Pass.
9. Product-owner voice — Pass contingent on execution.
10. Structure and concision — Pass.

## Remaining Gaps
- No explicit requirement that Implementer cite verification sources for auditability.
- "Directional language" needs a floor: ≥2–3 concrete verifiable signals.
- Risks section should require ≥3 distinct categories.
- Competitor framing space should be named explicitly.

Score: 9/10

---

## Planner — Iteration 3

# Iteration 3 Plan: `interview_solution.md` — Durable Agent Hosting Model for .NET 12

## Theme (unchanged)
First-class **Durable Agent Hosting Model** in .NET 12: runtime + framework primitive for long-running, stateful, LLM-driven agent workloads with built-in durability, replayable execution, tool/permission sandboxing, and first-class observability.

This iteration carries forward all iteration 2 structure, the verification step, the forbidden-claims list, and hedged language. Adds: inline source citations, concrete verifiable signals, diversified risks, explicit competitor framing.

## Pre-Write Verification Step
Implementer must run WebFetch/WebSearch to confirm/refute each item below. If unverifiable: remove, hedge, or cite.

### Forbidden-claims list (verify NOT duplicated)
- Native AOT improvements / trimming for ASP.NET Core
- `Microsoft.Extensions.AI` abstractions (IChatClient, IEmbeddingGenerator, etc.)
- Semantic Kernel agent framework features
- Existing Orleans grains / Dapr workflows / Durable Task Framework
- OpenTelemetry GenAI semantic conventions support already merged
- Anything explicitly listed in .NET 10 GA notes or .NET 11 preview themes

Verification anchors: learn.microsoft.com/whats-new/dotnet-10, devblogs.microsoft.com/dotnet, github.com/dotnet/core, github.com/dotnet/extensions, github.com/microsoft/semantic-kernel.

The Implementer must cite at least one source URL inline (or in HTML comment) for every .NET 10 or .NET 11 reference.

### Concrete signals (≥2, ideally 3)
Surface 2–3 verifiable signals from:
- GitHub issues/discussions in `dotnet/extensions`, `microsoft/semantic-kernel`, `dotnet/aspnetcore`
- .NET Conf 2025 sessions
- Build 2025 announcements
- Stack Overflow / JetBrains / RedMonk surveys
- Azure AI Foundry / Azure AI Agent Service announcements
- LangGraph / CrewAI / AutoGen / Temporal release notes

If <2 signals can be verified, escalate, do not fabricate.

## Document Structure (13 sections)
1. Header — Title, author, date, target release, one-line thesis
2. Executive Summary (≤150 words) — single priority, named user, named outcome
3. The Single Top Priority — bolded sentence + 2–3 sentences clarifying scope
4. Why Now — must contain 2–3 cited concrete signals (Market, Ecosystem, Strategic)
5. Concrete Pain Points — 4–6 bullets, each with scenario + DIY workaround + insufficiency; ≥1 referencing real GH issue if found
6. Proposal: Durable Agent Hosting Model
   - 6.1 Core primitives — `IDurableAgent`, `AgentHost`, `AgentCheckpointStore`, `IToolInvoker`
   - 6.2 Programming model — sketch
   - 6.3 Runtime guarantees — at-least-once, deterministic replay, crash recovery, pause/resume, time-travel debugging
   - 6.4 Integration points — `Microsoft.Extensions.AI`, Semantic Kernel, ASP.NET Core minimal hosting, OTel GenAI conventions
   - 6.5 Explicit non-goals
7. Competitive & Ecosystem Landscape — 4 explicitly-named spaces:
   - Python agent runtimes (LangGraph, CrewAI, AutoGen)
   - JVM/general-purpose durable execution (Temporal, Restate)
   - Hyperscaler agent services (AWS Bedrock Agents, Google Vertex AI Agent Builder)
   - Microsoft's own surface (SK, M.E.AI, Azure AI Agent Service, Orleans/Dapr)
8. Microsoft Strategy Alignment — Copilot, Azure AI Foundry, "AI for every developer"
9. Risks & Mitigations — ≥3 distinct categories:
   - Technical (deterministic replay, checkpoint perf, AOT compat)
   - Adoption (SK overlap, "yet another framework," Python default)
   - Organizational (Azure AI Agent Service overlap, ownership, deprioritization)
   - Optional 4th: ecosystem fragmentation
10. Success Metrics — ≥5, measurable, time-bound (≥3 ref customers, ≥10K NuGet/mo, p95 <50ms, etc.)
11. Roadmap — Preview 1 ~Feb 2027 → mid-cycle → GA Nov 2027 (use "target")
12. Open Questions — 3–5 honest questions
13. Appendix: Verification Log — sources consulted, forbidden-claims checks, signals cited, hedged claims

## Voice & Style Rules
- Executive PO tone, first-person plural
- No marketing fluff, no emoji, no fabricated quotes
- Every .NET 10/11 reference cited inline
- 900–1400 words excluding appendix
- Markdown only; H2/H3, bullets, ≤1 table

## Implementer Checklist
1. Run Pre-Write Verification Step.
2. Gather ≥2 (ideally 3) concrete signals with citable URLs.
3. Confirm no forbidden-claims overlap.
4. Write `interview_solution.md` following 13-section structure.
5. Ensure single priority unambiguous, all 4 competitor spaces named, all 3 risk categories covered.
6. Append Verification Log.
7. Re-read end-to-end against the 10 review criteria.

## Critique Responses
- Auditability (Iter 2 gap 1): Inline citation requirement added; verification anchors named.
- Concrete signals (Iter 2 gap 2): ≥2–3 signals required from named source list, with escalation rule.
- Risk diversity (Iter 2 gap 3): ≥3 categories required (Technical, Adoption, Organizational).
- Competitor framing (Iter 2 gap 4): 4 explicitly-named comparison spaces required.

---

## Reviewer — Iteration 3

# Critique — Iteration 3

The plan has incorporated all four pieces of feedback from Iteration 2 in a concrete, enforceable way.

**1. Single top priority** — Pass.
**2. Avoids shipped .NET 10/11 features** — Strong. Forbidden-claims list explicit and broad, combined with inline-citation requirement and named verification anchors. Auditability now enforceable.
**3. Evidence-based rationale** — Strong. ≥2–3 verifiable signals with explicit escalation if <2 found. Verification Log appendix gives single audit point.
**4. Ecosystem/competitor awareness** — Strong. Four explicitly-named comparison spaces. Microsoft's own surface as a competitor space pre-empts "why not just SK/Azure AI Agent Service" pushback.
**5. Microsoft strategy alignment** — Pass. Organizational risk category forces engagement with Azure AI Agent Service team overlap.
**6. Concrete pain points** — Pass. Real GH issues with no-fabrication escalation.
**7. Risks/mitigations** — Strong. Three distinct categories with specific items, mitigations named.
**8. Measurable success metrics** — Strong. Five numbered, time-bound metrics with concrete examples.
**9. Credible PO voice** — Pass.
**10. Well-structured, concise** — Pass.

**Residual concerns (none blocking):**
- Verification Log appendix word-count ambiguity — Implementer should exclude from word count.
- "Preview 1 ~Feb 2027 → GA Nov 2027" should be flagged as assumption.

Both minor. The plan is enforceable end-to-end: every fabrication risk has a named guardrail, every gap has a concrete requirement, the Implementer has an auditable checklist.

Score: 10/10

---

## Implementer — Final

**File written:** `interview_solution.md`

**Pre-write verification performed:**
- .NET 10 GA notes — no in-box durable agent runtime shipped
- .NET 11 Preview 1 — no AI/agent/durable-execution themes
- Microsoft Agent Framework (`Microsoft.Agents.AI`) — confirmed as out-of-band NuGet, NOT part of .NET 10 or 11

**Critical pivot:** Discovered `Microsoft.Agents.AI.DurableTask` already exists as a preview NuGet. Reframed proposal from "build a new durable agent framework" to **"promote the still-preview durable agent runtime into a first-class, in-box, AOT-friendly, OTel-stable .NET 12 platform primitive."**

**Concrete signals gathered (3, all citable inline):**
1. Ecosystem: `microsoft/semantic-kernel#13435` — durable execution / replay / audit gaps for long-running SK agent workflows (filed 2026-01-02, open)
2. Strategic: Azure AI Foundry Agent Service GA + Build/Ignite 2025 announcements
3. Market: `Microsoft.Agents.AI` 1.0.0-rc4 and `Microsoft.Agents.AI.DurableTask` 1.0.0-preview still in preview as of April 2026

**Document structure:** 12 main sections covering all 13 plan sections. Single top priority unambiguous. All four competitor spaces named. All three risk categories covered (plus ecosystem fourth). 7 measurable success metrics. Roadmap uses "target" everywhere. 5 Open Questions.

**Hedged where unverifiable:** Roadmap dates, customer/download counts, .NET Conf 2027 ISV counts, OTel GenAI stabilization timeline.

**Sources cited inline:**
- learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview
- devblogs.microsoft.com/dotnet/dotnet-11-preview-1/
- github.com/microsoft/agent-framework
- github.com/microsoft/semantic-kernel/issues/13435
- techcommunity.microsoft.com/blog/azure-ai-foundry-blog/foundry-agent-service-at-ignite-2025
- azure.microsoft.com/en-us/blog/actioning-agentic-ai-5-ways-to-build-with-news-from-microsoft-ignite-2025/
- nuget.org/packages/Microsoft.Agents.AI/
- nuget.org/packages/Microsoft.Agents.AI.DurableTask/1.0.0-preview.260225.1
- opentelemetry.io/docs/specs/semconv/gen-ai/

No implementation commands were specified.
