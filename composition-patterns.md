# WSO2 Composition Patterns — How the Pieces Snap Together

> These are **product-agnostic building blocks**, not a prescribed architecture and not tied to any
> industry. Pick the patterns that fit *your* idea and compose them. Each pattern names the WSO2
> products involved, what problem it solves, and the honest "why this and not just glue code"
> justification. Combine freely.
>
> Read `wso2-hackathon-context-pack.md` first for product definitions. Every claim here is
> version-sensitive — verify against the live docs before you build.

---

## How to read these

Each pattern has: **the shape**, **what it buys you**, **products**, and **do-it-yourself vs. WSO2**
(so you understand what you'd otherwise hand-build). Patterns stack — most good submissions use 2–4.

---

## Pattern A — "Governed brain": agent + LLM Gateway

**The shape.** Your agent never calls OpenAI/Anthropic/etc. directly. It calls the **AI Gateway's
LLM Proxy**, which fronts one or more providers.

**What it buys you.** Multi-provider routing + failover (provider goes down → traffic reroutes),
**token-based rate limiting** (cap runaway agent spend), **semantic caching** (repeat questions don't
re-bill), and **guardrails** (PII masking, prompt-injection protection, content safety) applied
centrally instead of scattered in prompt code.

**Products.** API Platform (AI Gateway / LLM Proxy). Optionally → Moesif for cost dashboards.

**DIY vs. WSO2.** DIY means writing your own retry/failover, a token accountant, a cache layer, and
prompt/response scrubbing — per provider. The gateway makes it config.

---

## Pattern B — "Hands": enterprise APIs → MCP tools

**The shape.** You have existing REST APIs (or mock ones). The **MCP Gateway auto-generates MCP
tools from their OpenAPI specs**. Your agent discovers and calls those tools over MCP — with auth,
rate limits, and an audit trail — without you writing an MCP server.

**What it buys you.** Your agent can *act* (query records, submit transactions, look things up)
against real systems, and every action is authenticated and logged. This is the difference between a
chatbot that talks and an assistant that does things.

**Products.** API Platform (MCP Gateway). Pairs naturally with Pattern A (LLM decides, MCP acts).

**DIY vs. WSO2.** DIY means hand-writing and hosting an MCP server per API, plus your own
authz/throttling/logging on tool calls. The gateway derives tools from the spec and governs them.

---

## Pattern C — "Memory & grounding": RAG inside the Integrator

**The shape.** Build a RAG pipeline in **WSO2 Integrator**: ingest documents → chunk → embed → store
in a vector DB (Weaviate/Milvus/Pinecone per profile) → at query time, retrieve + feed the LLM via
the RAG Chat capability. Add persistent conversation memory.

**What it buys you.** Grounded, source-cited answers instead of hallucinated ones; the assistant
knows *your* domain content. Memory makes multi-turn conversations coherent.

**Products.** Integration Platform (WSO2 Integrator: agent framework, RAG, memory). Optionally send
LLM calls through Pattern A's gateway.

**DIY vs. WSO2.** DIY means wiring an embedding pipeline, a vector store client, a retrieval step,
and prompt assembly by hand. Integrator gives you mediators/operations for each step, low-code or
pro-code.

---

## Pattern D — "Who are you, and what may you do": identity for users *and* agents

**The shape.** Users authenticate via **Identity Platform** (OAuth2/OIDC). The **agent itself gets
an identity via Agent ID**, and tool calls carry verifiable identity so you can authorize *what the
agent is allowed to do* — enforced at tool-execution time.

**What it buys you.** Consent and scopes for user-facing actions; and the genuinely interesting
governance story — an autonomous agent that is a first-class, authorizable principal, not an
anonymous script with a shared key.

**Products.** Identity Platform (Agent ID, MCP authorization). Integrator has built-in Agent ID
integration; API Platform gateways enforce the tokens.

**DIY vs. WSO2.** DIY means a login stack plus inventing your own scheme for "which agent is this and
is it allowed." Agent ID makes it standard identity.

---

## Pattern E — "Watch and grade": observability + evals

**The shape.** Run/observe your agent through the **Agent Manager Platform**: OpenTelemetry traces,
metrics, and logs; zero-code auto-instrumentation for LangChain/LlamaIndex; and **evals** (assertions
or LLM-as-judge) to check the agent behaves. WSO2 Integrator also has a built-in LLM evaluation
framework and an Agent Execution Visualizer.

**What it buys you.** You can *show the judges* what your agent actually did — which tools it called,
in what order, where it went wrong — and evidence that it behaves correctly. Non-deterministic AI is
hard to trust without this.

**Products.** Agent Manager Platform (pre-GA) and/or Integrator's eval framework. Built on OpenChoreo.

**DIY vs. WSO2.** DIY means instrumenting every framework call and building an eval harness. Auto-
instrumentation + built-in evals collapse that.

---

## Pattern F — "Show me the money/usage": analytics via Moesif

**The shape.** Configure the AI Gateway to emit analytics to **Moesif** (`MOESIF_KEY`). Get
dashboards on AI cost, call volume, and usage patterns; optionally a monetization model.

**What it buys you.** A cost/usage story and a business angle — useful for the "actionable
experiences" and any monetization narrative.

**Products.** Moesif (SaaS) fed by API Platform AI Gateway.

**DIY vs. WSO2.** DIY means building metering + a dashboard. Moesif is the drop-in.

---

## Pattern G — "Somewhere to live": hosting on OpenChoreo

**The shape.** Deploy your components (agent, integrations, services) on **OpenChoreo**, the
Kubernetes-native platform. If you use Agent Manager, you're already on OpenChoreo underneath.

**What it buys you.** A credible production hosting story rather than "it runs on my laptop."

**Products.** OpenChoreo.

---

## Putting patterns together — three honest tiers

These are **illustrations of how the blocks combine**, not templates to copy. Your idea should drive
which blocks you pick.

**Tier 1 — Real and focused (2 products).**
Pattern C (RAG agent in Integrator) + Pattern A (LLM Gateway). A grounded assistant with cost control
and guardrails. Fully local, no SaaS required. A complete, defensible submission.

**Tier 2 — Acts on the world (3 products).**
Add Pattern B (MCP tools from your APIs). Now the assistant *does* things against real systems, with
audit trails. This is where "Enterprise AI Assistant" starts to mean something.

**Tier 3 — Governed, observed, agentic enterprise (4–5 products).**
Add Pattern D (Agent ID + user login) and Pattern E (Agent Manager observability/evals), optionally
Pattern F (Moesif) and Pattern G (OpenChoreo hosting). This is the full bonus-points play — but only
if each product is doing real work in your flow.

> **The judging test to apply to yourself:** for every WSO2 product in your diagram, can you delete
> it and have your assistant still work? If yes, it's decoration — either make it load-bearing or
> drop it. Judges reward *meaningful* integration, not logo count.

---

## Common wiring mistakes to avoid

- **Calling LLMs directly "just for the demo"** and bolting the gateway on later — you lose the whole
  point of Pattern A and it rarely gets reconnected. Route through the gateway from hour one.
- **Hand-writing an MCP server** when the MCP Gateway can generate one from your OpenAPI spec.
- **Treating Agent ID as an afterthought.** The agent-identity governance story is a differentiator;
  design for it early if you're going for bonus points.
- **Assuming hybrid/cloud gateway works in your region.** Verify region availability; default to the
  standalone gateway for a clean local/on-prem run.
- **Proposing WSO2 BPS** for workflow. It's deprecated — use a partner engine and integrate to it.

---

*Verify all version-gated specifics against the live docs indexed in the context pack before building.*
