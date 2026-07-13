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
> **How to use it.** Paste this whole file in first. Then paste `composition-patterns.md` if you
> want integration patterns, and follow `day-zero-quickstart.md` to get something running. If your
> tool supports skills/rules files, also install `wso2-ai-assistant.skill.md`.

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
| **WSO2 Integrator** | "MI" alone, "BI" alone | 5.0.0 (H1 2026) **unified BI + MI into one product**. |
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
- **5.0.0 (H1 2026) unified BI and MI into one "WSO2 Integrator."**
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
- **Deploy:** on-prem cleanly. Latest IS docs: https://is.docs.wso2.com/en/latest/

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
you compose deliberately, not randomly. Full patterns are in `composition-patterns.md`; the summary:

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

**Minimum interesting stack (2 products):** Integrator (build the agent + RAG) + API Platform AI
Gateway (govern LLM + expose enterprise APIs as MCP tools).

**Bonus-points stack (4–5 products):** add Identity Platform (Agent ID + user login) + Agent Manager
(host/observe/eval) + Moesif (cost dashboards). Only add a product if it does real work — judges can
tell decoration from integration.

---

## 4. Anti-hallucination protocol (read this to your model, and to yourself)

These products are past your model's training cutoff. Enforce this behavior:

1. **Never invent a WSO2 CLI flag, config key, connector name, REST path, or product feature.** If
   you cannot cite it from a doc URL in this pack or from a live docs fetch, say "I need to check the
   docs" and stop.
2. **Product names must match Section 1 exactly.** If you catch yourself writing "API Manager" as if
   it were the whole product, or "MI" as a standalone product, correct it.
3. **Version-gate every claim.** State the version you're assuming (e.g. "Integrator 5.0.0 / MI
   4.6.0", "API Platform gateway v1.0.0", "Agent Manager v0.18.x"). If the user is on a different
   version, flag that the details may differ.
4. **Prefer `latest` docs URLs** (URLs containing `/latest/`) and the canonical domains:
   `wso2.com/*/docs`, `*.docs.wso2.com`, `wso2.github.io/agent-manager`, `openchoreo.dev/docs`,
   `moesif.com/docs`, `github.com/wso2/*`.
5. **When in doubt, fetch.** A 20-second doc fetch beats a plausible-but-wrong config that costs the
   team an hour of debugging during a timed hackathon.
6. **Distinguish GA from pre-GA.** Agent Manager is pre-GA; do not present its behavior as stable.

---

## 5. Fast links index (give these to your model as the source of truth)

**API Platform**
- Overview & get-started: https://wso2.com/api-platform/docs/get-started/
- AI Gateway: https://wso2.com/api-platform/ai-gateway/
- Standalone gateway getting-started: https://wso2.com/api-platform/docs/api-gateway/1.1.0/quick-start-guide/
- MCP Gateway get-started: https://wso2.com/api-platform/docs/ai-gateway/1.1.0/mcp-proxy/quick-start-guide/
- GitHub: https://github.com/wso2/api-platform

**Integration Platform / WSO2 Integrator**
- Docs home: https://wso2.com/integration-platform/docs/
- Build first AI integration (chatbot → KB → RAG → agent): https://wso2.com/integration-platform/docs/genai/tutorials/it-helpdesk-chatbot
- 5.0.0 release notes: https://wso2.com/library/blogs/wso2-integrator-5-0-0-release/

**Identity Platform**
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

## 6. A good first prompt to your AI coding tool

> "I'm building an Enterprise AI Assistant for the WSO2 hackathon. I've loaded the WSO2 AI Context
> Pack. Before writing any WSO2 config or code, confirm the product names and versions you'll target
> from Section 1, and tell me which docs URL you'll verify against. My assistant should: `<one
> sentence about your use case>`. Propose the smallest WSO2 stack that makes it real, then a
> bonus-points version. Do not invent WSO2 APIs — fetch docs when unsure."

---

*Product facts verified against WSO2 documentation and
release announcements current to mid-2026. Because these products move fast, re-verify version-gated
details against the live docs linked above.*
