# WSO2 Hackathon — AI Context Pack

> **What this file is.** A single, self-contained primer on the WSO2 product stack for building
> an Enterprise AI Assistant. It is written to be pasted into an AI coding assistant
> (Claude, Cursor, Codex, etc.) at the start of a session, and to be read by a human in one sitting.
>
> **Why it exists.** Every WSO2 product below shipped or changed in 2026. They are **newer than the
> training cutoff of every current AI model**, including the one you are probably talking to. That
> means your model *will confidently invent* WSO2 APIs, CLI flags, config keys, and product names
> that do not exist. This pack gives the model an accurate mental model and — most importantly —
> tells it **when to stop guessing and go read the live docs.** Treat the linked docs as ground
> truth; treat this pack as the map.
>
> **How to use it.** Paste this whole file into your AI tool first — it contains everything: the
> mental model, exact naming, per-product guidance, composition patterns, setup commands, a links
> index, and an anti-hallucination protocol. If your tool supports skills/rules files, also install
> the skill file (`.claude/skills/wso2-ai-assistant/SKILL.md`; see the README for per-tool install). The
> `README.md` is the human-readable companion to this file.

---

## 0. The one-paragraph mental model

WSO2 gives you an **agentic-enterprise stack**: a place to *build* AI agents and integrations
(**Integration Platform**), a place to *govern and expose* APIs, LLMs, and agent tools
(**API Platform**, whose **AI Gateway** does LLM + MCP governance), a place to handle *identity*
for humans **and agents** (**Identity Platform**, incl. Agent ID), a place to *run, observe, and
evaluate* agents at scale (**Agent Manager Platform**), a Kubernetes-native place to *host* it all
(**OpenChoreo**), and an *analytics* layer for all AI/API traffic (**Moesif**). You do **not** need
all of them. Pick the two or three that make your assistant real, and wire them together.

---

## 1. Naming discipline (do this or your docs searches will fail)

Model output tends to drift to old names. Use the **exact** current names — they are also the
correct search terms for the docs:

| Correct current name | Do NOT call it | Notes |
|---|---|---|
| **WSO2 API Platform** | "WSO2 API Manager" (that's now one deployment mode of it) | GA March 2026. Modular. |
| **AI Gateway** (part of API Platform) | "AI Manager" | Two halves: **LLM Proxy** + **MCP Proxy/Gateway**. |
| **MCP Gateway** / **MCP Proxy** | "MCP server manager" | Turns REST APIs into MCP tools; governs MCP traffic. |
| **WSO2 Integration Platform** | "EI", "ESB" | Contains **WSO2 Integrator** (unified runtime). |
| **WSO2 Integrator** | "MI" alone, "BI" alone | 5.0.0 (H1 2026) **unified BI + MI into one product**. **Default profile runs on Ballerina**; the MI profile still exists but is not the encouraged path. |
| **WSO2 Identity Platform/Server** | "IS" alone | Includes **Agent ID** for agent identity. |
| **Agent Manager Platform** | "Choreo agents" | Pre-GA (v0.18.x as of mid-2026). Built on OpenChoreo. |
| **OpenChoreo** | "Choreo" (that's the SaaS; OpenChoreo is the OSS platform) | Kubernetes-native, open source. |
| **Moesif** | "WSO2 Analytics", "DAS" | SaaS only. AI/API analytics + monetization. Acquired by WSO2 in 2025. |

---

## 2. What each product actually does (and when to reach for it)

### 2.1 WSO2 API Platform — *govern and expose APIs, LLMs, and agent tools*
- **The core idea:** one control plane for traditional APIs **and** AI assets (LLM models,
  MCP servers).
- **AI Gateway** has two capabilities you will actually use:
  - **LLM Proxy (LLM Gateway):** a single endpoint in front of OpenAI, Anthropic, Azure OpenAI,
    Mistral, AWS Bedrock. Gives you **multi-provider routing/failover, token-based rate limiting,
    semantic caching, and guardrails** (PII masking, prompt-injection protection, content safety).
    This is how you stop one runaway agent from burning your whole token budget.
  - **MCP Gateway (MCP Proxy):** **auto-generates an MCP server from any REST API's OpenAPI spec**,
    so an agent can call your existing enterprise APIs as MCP tools — with auth, rate limits, and a
    full audit trail — without you hand-writing an MCP server. Also governs *third-party* MCP servers.
- **Gateway runtime:** Go-based, single binary/container. Runs **standalone** (YAML/CLI/REST, no UI,
  fully local — *this is your on-prem-friendly, no-signup path*) or **connected to a control plane**
  (web UI, https://console.bijira.dev/login).
- **Reach for it when:** your assistant calls LLMs (you want cost control + guardrails), OR your
  assistant needs to safely call enterprise APIs as tools (MCP), OR you want one audited entry point
  for all AI traffic.
- **Docs:** https://wso2.com/api-platform/docs/ · GitHub: https://github.com/wso2/api-platform

### 2.2 WSO2 Integration Platform (WSO2 Integrator) — *build agents, RAG, and integrations*
- **The core idea:** low-code **and** pro-code environment (100% parity) to build integrations and
  **AI agents** directly inside integration flows.
- **5.0.0 (H1 2026) unified BI and MI into one "WSO2 Integrator."** The **default profile runs on
  Ballerina** (this is the encouraged path, and what the GenAI docs use); the **MI profile** is still
  available but not the recommended default. Build the AI/agent work against the GenAI docs, not MI-only docs.
- **AI capabilities you get out of the box:**
  - **Agent framework** with **MCP support**, persistent memory, summarizing/trimming, and RAG.
  - **RAG**: knowledge bases + vector DBs (Weaviate, Milvus, Pinecone, pgvector in various profiles/tutorials),
    embedding models, "RAG Chat" mediator.
  - **Built-in LLM evaluation framework** (collect traces → build datasets → assertions or
    LLM-as-judge) and an **Agent Execution Visualizer** to see which tools an agent called and why.
  - **Built-in Agent ID integration** for agent identity/access, enforced at tool-execution time.
  - **600+ connectors** to SaaS, DBs, messaging, and AI.
  - **Copilot** / Agent mode: describe a requirement in natural language, it scaffolds the project.
- **Reach for it when:** you are actually *building the agent* / orchestration / RAG pipeline, or
  connecting to enterprise systems (DBs, SaaS, legacy SOAP, files, events).
- **Deploy:** on-prem via containers or SaaS. Develop in WSO2 Integrator IDE (https://wso2.com/products/downloads/?product=wso2integrator).
- **Docs:** https://wso2.com/integration-platform/docs/

### 2.3 WSO2 Identity Platform — *identity for humans and agents*
- **The core idea:** CIAM + workforce IAM (OAuth2/OIDC/SAML, MFA, adaptive auth, federation) **plus
  Agent ID** — first-class identity for AI agents so you can authorize *what an agent is allowed to
  do*, and **MCP authorization** so tool calls carry real, verifiable identity.
- **Reach for it when:** your assistant acts on behalf of a user (login, consent, scopes), or your
  agent needs its own identity to be governed.
- **Deploy:** on-prem (self-managed Identity Server) or SaaS — both work; pick by your machine's
  resources. Platform docs: https://wso2.com/identity-platform/docs/ · Latest IS docs:
  https://is.docs.wso2.com/en/latest/

### 2.4 Agent Manager Platform — *run, observe, evaluate, secure agents at scale*
- **The core idea:** an open control plane to **deploy agents on Kubernetes**, get **full-stack
  observability** (OpenTelemetry traces/metrics/logs), enforce **governance**, and do **evals** —
  for both internally hosted and externally deployed agents.
- **Auto-instrumentation** (zero-code / some manual code).
- **Built on OpenChoreo.** **Pre-GA (v0.18.x mid-2026).** SaaS console exists; on-prem quick-start
  is a Docker dev container.
- **Reach for it when:** you want to *host + observe + evaluate* your agent as a bonus-points play,
  especially if you built the agent in a framework (LangChain/LlamaIndex) rather than in Integrator.
- **Docs:** https://wso2.github.io/agent-manager/ · GitHub: https://github.com/wso2/agent-manager
  · Cloud console: https://console.agent-manager.cloud.wso2.com/

> ⚠️ Pre-GA. Expect rough edges and fast-moving docs. Pin the version (e.g. v0.18.x) in your notes
> and re-check the quick-start command each session.

### 2.5 OpenChoreo — *Kubernetes-native hosting/IDP*
- **The core idea:** open-source, Kubernetes-native internal developer platform to deploy and manage
  cloud-native apps (the OSS engine under Choreo and under Agent Manager).
- **Reach for it when:** you want a real hosting story for your components, or you're already using
  Agent Manager (which sits on it). Not primarily an "AI build" tool.
- **Docs:** https://openchoreo.dev/docs/

### 2.6 Moesif — *AI/API analytics and monetization*
- **The core idea:** SaaS analytics for API + AI traffic — usage, cost of AI calls, developer
  behavior, revenue. The API Platform's AI Gateway can **push analytics to Moesif**.
- **Reach for it when:** you want to *show* AI cost/usage dashboards, or a monetization angle.
- **Deploy:** SaaS only — configure your gateway to emit to it (`MOESIF_KEY`).
- **Docs:** https://www.moesif.com/docs

---

## 3. How the products compose (the part no single doc gives you)

The judges reward **meaningful multi-product integration.** Here is the honest dependency picture so
you compose deliberately, not randomly.

```
                       ┌─────────────────────────────────────────────┐
   end user / channel  │  chat UI, WhatsApp, USSD, web, mobile        │
                       └───────────────────┬─────────────────────────┘
                                           │ (user identity, consent)
                             ┌─────────────▼──────────────┐
                             │   WSO2 Identity Platform    │  humans + Agent ID
                             └─────────────┬──────────────┘
                                           │
                    ┌──────────────────────▼───────────────────────┐
                    │              THE AGENT / ORCHESTRATOR          │
                    │  built in WSO2 Integrator (agent framework,    │
                    │  RAG, memory, MCP)  — or your own LangChain/   │
                    │  LlamaIndex app                                 │
                    └───────┬───────────────────────────────┬───────┘
                            │ LLM calls                      │ tool calls (MCP)
              ┌─────────────▼────────────┐      ┌────────────▼─────────────────┐
              │  API Platform AI Gateway  │      │  API Platform MCP Gateway     │
              │  (LLM Proxy): routing,    │      │  (MCP Proxy): REST→MCP tools, │
              │  guardrails, token limits │      │  auth, audit                  │
              └─────────────┬────────────┘      └────────────┬─────────────────┘
                            │                                │
                   ┌────────▼─────────┐            ┌─────────▼──────────┐
                   │ LLM providers    │            │ your enterprise    │
                   │ (OpenAI, etc.)   │            │ REST APIs / systems│
                   └──────────────────┘            └────────────────────┘

   observability + evals:  Agent Manager Platform  (traces, metrics, evals)
   analytics + cost:       Moesif  (fed by AI Gateway)
   hosting:                OpenChoreo (K8s)
```

### The composition patterns (product-agnostic building blocks)

These are **building blocks, not a prescribed architecture** and not tied to any industry. Pick the
ones that fit *your* idea and combine them — most good submissions use 2–4. Each names the products
involved, what it buys you, and the honest "why this and not just glue code" justification.

**Pattern A — "Governed brain": agent + LLM Gateway.**
Your agent never calls OpenAI/Anthropic/etc. directly; it calls the **AI Gateway's LLM Proxy**, which
fronts one or more providers. *Buys you:* multi-provider routing + failover, **token-based rate
limiting** (cap runaway spend), **semantic caching** (repeat questions don't re-bill), and
**guardrails** (PII masking, prompt-injection protection, content safety) applied centrally. *Products:*
API Platform (AI Gateway / LLM Proxy); optionally → Moesif for cost dashboards. *DIY vs. WSO2:* DIY means
writing your own retry/failover, a token accountant, a cache layer, and prompt/response scrubbing per
provider. The gateway makes it config.

**Pattern B — "Hands": enterprise APIs → MCP tools.**
The **MCP Gateway auto-generates MCP tools from your REST APIs' OpenAPI specs**; your agent discovers and
calls those tools over MCP — with auth, rate limits, and an audit trail — without you writing an MCP
server. *Buys you:* the agent can *act* against real systems, every action authenticated and logged. This
is the difference between a chatbot that talks and an assistant that does things. *Products:* API Platform
(MCP Gateway); pairs naturally with Pattern A (LLM decides, MCP acts). *DIY vs. WSO2:* DIY means
hand-writing and hosting an MCP server per API plus your own authz/throttling/logging on tool calls.

**Pattern C — "Memory & grounding": RAG inside the Integrator.**
Build a RAG pipeline in **WSO2 Integrator**: ingest → chunk → embed → store in a vector DB
(Weaviate/Milvus/Pinecone per profile) → at query time retrieve + feed the LLM via the RAG Chat
capability; add persistent conversation memory. *Buys you:* grounded, source-cited answers instead of
hallucinated ones; the assistant knows *your* domain content, and memory makes multi-turn coherent.
*Products:* Integration Platform (agent framework, RAG, memory); optionally send LLM calls through Pattern
A's gateway. *DIY vs. WSO2:* DIY means wiring an embedding pipeline, vector store client, retrieval step,
and prompt assembly by hand. Integrator gives you mediators/operations for each step.

**Pattern D — "Who are you, and what may you do": identity for users *and* agents.**
Users authenticate via **Identity Platform** (OAuth2/OIDC); the **agent itself gets an identity via Agent
ID**, and tool calls carry verifiable identity so you can authorize *what the agent is allowed to do* —
enforced at tool-execution time. *Buys you:* consent and scopes for user-facing actions, plus the genuinely
interesting governance story — an autonomous agent as a first-class, authorizable principal, not an
anonymous script with a shared key. *Products:* Identity Platform (Agent ID, MCP authorization); Integrator
has built-in Agent ID integration; API Platform gateways enforce the tokens. *DIY vs. WSO2:* DIY means a
login stack plus inventing your own scheme for "which agent is this and is it allowed."

**Pattern E — "Watch and grade": observability + evals.**
Run/observe your agent through the **Agent Manager Platform**: OpenTelemetry traces, metrics, logs;
auto-instrumentation; and **evals** (assertions or LLM-as-judge). WSO2 Integrator also has a built-in LLM
evaluation framework and an Agent Execution Visualizer. *Buys you:* you can *show the judges* what your
agent actually did — which tools it called, in what order, where it went wrong — plus evidence it behaves
correctly. Non-deterministic AI is hard to trust without this. *Products:* Agent Manager Platform (pre-GA)
and/or Integrator's eval framework; built on OpenChoreo. *DIY vs. WSO2:* DIY means instrumenting every
framework call and building an eval harness.

**Pattern F — "Show me the money/usage": analytics via Moesif.**
Configure the AI Gateway to emit analytics to **Moesif** (`MOESIF_KEY`): dashboards on AI cost, call
volume, and usage patterns; optionally a monetization model. *Buys you:* a cost/usage story and a business
angle. *Products:* Moesif (SaaS) fed by API Platform AI Gateway. *DIY vs. WSO2:* DIY means building metering
+ a dashboard.

**Pattern G — "Somewhere to live": hosting on OpenChoreo.**
Deploy your components (agent, integrations, services) on **OpenChoreo**, the Kubernetes-native platform.
If you use Agent Manager, you're already on OpenChoreo underneath. *Buys you:* a credible production
hosting story rather than "it runs on my laptop." *Products:* OpenChoreo.

### Three honest tiers (illustrations, not templates)

Your idea should drive which blocks you pick.

- **Tier 1 — Real and focused (2 products).** Pattern C (RAG agent in Integrator) + Pattern A (LLM
  Gateway). A grounded assistant with cost control and guardrails. Fully local, no SaaS required.
- **Tier 2 — Acts on the world (3 products).** Add Pattern B (MCP tools from your APIs). Now the assistant
  *does* things against real systems, with audit trails. This is where "Enterprise AI Assistant" starts to
  mean something.
- **Tier 3 — Governed, observed, agentic enterprise (4–5 products).** Add Pattern D (Agent ID + user login)
  and Pattern E (Agent Manager observability/evals), optionally Pattern F (Moesif) and Pattern G (OpenChoreo
  hosting). The full bonus-points play — but only if each product is doing real work in your flow.

> **The judging test to apply to yourself:** for every WSO2 product in your diagram, can you delete it and
> have your assistant still work? If yes, it's decoration — either make it load-bearing or drop it. Judges
> reward *meaningful* integration, not logo count.

---

## 4. Day-zero setup (get something running in the first hour)

This is a **menu, not a golden path** — do only the sections your idea needs. **Both local/standalone
and SaaS/cloud paths are valid** — pick by your machine's resources: standalone/on-prem gives full
control and no signup; SaaS/cloud is easier if you're resource-constrained. Commands are **pinned to
mid-2026 releases** and versions move — if a download 404s, grab the current version from the linked
docs; never hand-edit a version into a URL and hope.

**Prerequisites:** Docker + Docker Compose (≥ 8 GB RAM if running several components), Java 17+ (JVM
runtimes), WSO2 Integrator IDE (if building agents), Node.js 18+ / Python 3.10+ (your own code), and an LLM
provider API key — put it *behind* the AI Gateway, not in agent code.

**1. API Platform — AI Gateway (LLM + MCP governance).** The **standalone** gateway (no control-plane
signup, configured by YAML/CLI) is the simplest local start; the **control-plane-connected** mode adds
a web UI if you prefer SaaS. Get the current commands from
https://wso2.com/api-platform/docs/get-started/ (choose the *standalone AI gateway* quick start) and
https://github.com/wso2/api-platform (releases).

```bash
# Illustrative — confirm the current version tag on the releases page first.
curl -sLO https://github.com/wso2/api-platform/releases/download/gateway/v1.0.0/wso2apip-api-gateway-1.0.0.zip
unzip wso2apip-api-gateway-1.0.0.zip
cd wso2apip-api-gateway-1.0.0
```

Point the LLM Proxy at your provider and give your agent the gateway's endpoint. For MCP, feed it an
OpenAPI spec and it derives tools. To emit analytics to Moesif, the gateway config takes a `MOESIF_KEY`.

**2. WSO2 Integrator — build the agent / RAG / integrations.** Fastest dev loop is the WSO2 Integrator IDE
(agent framework, RAG, Copilot) on the **Ballerina default profile**. Start from the GenAI docs, not
MI-only docs. Docs home: https://wso2.com/integration-platform/docs/ · GenAI overview:
https://wso2.com/integration-platform/docs/genai/overview · First AI integration tutorial:
https://wso2.com/integration-platform/docs/genai/tutorials/it-helpdesk-chatbot

**3. Identity Platform — login + Agent ID.** Runs on-prem (self-managed Identity Server) or SaaS — pick
by resources. Configure an OAuth2/OIDC app for user login; for the agent-identity story look for
**Agent ID** and **MCP authorization**. Add only if login/consent or agent-identity governance is part
of your idea. Platform docs: https://wso2.com/identity-platform/docs/ · Latest IS docs:
https://is.docs.wso2.com/en/latest/

**4. Agent Manager Platform — host / observe / eval (pre-GA).** Quick-start via a Docker dev container
(version-pinned — check the docs for the current tag):

```bash
# Illustrative — confirm current version at the docs link before running.
docker run --rm -it --name amp-quick-start \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --network=host \
  ghcr.io/wso2/amp-quick-start:v0.18.0
# then, inside the container:
./install.sh
```

Quick start: https://wso2.github.io/agent-manager/ · Hosted console:
https://console.agent-manager.cloud.wso2.com/ · ⚠️ **Pre-GA — expect version/commands to change.**

**5. OpenChoreo — Kubernetes-native hosting (optional).** For a real hosting story, or if using Agent
Manager (built on it), follow https://openchoreo.dev/docs/ . You'll want a local Kubernetes
(k3d/kind/minikube) or a cluster.

**6. Moesif — analytics (SaaS, optional).** Sign up, get your project API key, set it as `MOESIF_KEY` in the
AI Gateway config (step 1). Docs: https://www.moesif.com/docs

---

## 5. Anti-hallucination protocol (read this to your model, and to yourself)

These products are past your model's training cutoff. Enforce this behavior:

1. **Never invent a WSO2 CLI flag, config key, connector name, REST path, or product feature.** If
   you cannot cite it from a doc URL in this pack or from a live docs fetch, say "I need to check the
   docs" and stop.
2. **Product names must match Section 1 exactly.** If you catch yourself writing "API Manager" as if
   it were the whole product, or "MI" as a standalone product, correct it.
3. **Version-gate every claim.** State the version you're assuming (e.g. "API Platform gateway v1.0.0", "Agent Manager v0.18.x"). If the user is on a different
   version, flag that the details may differ.
4. **Prefer `latest` docs URLs** (URLs containing `/latest/`) and the canonical domains:
   `wso2.com/*/docs`, `*.docs.wso2.com`, `wso2.github.io/agent-manager`, `openchoreo.dev/docs`,
   `moesif.com/docs`, `github.com/wso2/*`.
5. **When in doubt, fetch.** A 20-second doc fetch beats a plausible-but-wrong config that costs the
   team an hour of debugging during a timed hackathon.
6. **Distinguish GA from pre-GA.** Agent Manager is pre-GA; do not present its behavior as stable.

---

## 6. Fast links index (give these to your model as the source of truth)

**API Platform**
- Overview & get-started: https://wso2.com/api-platform/docs/get-started/
- AI Gateway: https://wso2.com/api-platform/ai-gateway/
- Standalone gateway getting-started: https://wso2.com/api-platform/docs/api-gateway/1.1.0/quick-start-guide/
- MCP Gateway get-started: https://wso2.com/api-platform/docs/ai-gateway/1.1.0/mcp-proxy/quick-start-guide/
- GitHub: https://github.com/wso2/api-platform

**Integration Platform / WSO2 Integrator** (Ballerina default profile — use the GenAI docs)
- Docs home: https://wso2.com/integration-platform/docs/
- GenAI overview (start here): https://wso2.com/integration-platform/docs/genai/overview
- First AI integration tutorial (chatbot → KB → RAG → agent): https://wso2.com/integration-platform/docs/genai/tutorials/it-helpdesk-chatbot
- 5.0.0 release notes: https://wso2.com/library/blogs/wso2-integrator-5-0-0-release/

**Identity Platform**
- Platform docs: https://wso2.com/identity-platform/docs/
- IS (latest): https://is.docs.wso2.com/en/latest/
- Agent ID example: https://is.docs.wso2.com/en/latest/quick-starts/agent-auth-py/

**Agent Manager Platform** (pre-GA)
- Docs: https://wso2.github.io/agent-manager/ · GitHub: https://github.com/wso2/agent-manager
- Cloud console: https://console.agent-manager.cloud.wso2.com/

**OpenChoreo**
- Docs: https://openchoreo.dev/docs/

**Moesif**
- Docs: https://www.moesif.com/docs

---

## 7. A good first prompt to your AI coding tool

> "I'm building an Enterprise AI Assistant for the WSO2 hackathon. I've loaded the WSO2 AI Context
> Pack. Before writing any WSO2 config or code, confirm the product names and versions you'll target
> from Section 1, and tell me which docs URL you'll verify against. My assistant should: `<one
> sentence about your use case>`. Propose the smallest WSO2 stack that makes it real, then a
> bonus-points version. Do not invent WSO2 APIs — fetch docs when unsure."

---

*Product facts verified against WSO2 documentation and
release announcements current to mid-2026. Because these products move fast, re-verify version-gated
details against the live docs linked above.*
