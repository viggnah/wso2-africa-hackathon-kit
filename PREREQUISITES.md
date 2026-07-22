# Attendee Prerequisites

Before the hackathon, please work through this checklist. It covers what to **know**, what **hardware**
you need, what **software** to install, and — only if your idea uses them — which **WSO2 products** to
set up in advance. You are free to build your AI Agent with any stack; the WSO2 section is optional and
only applies if you choose to use WSO2 products (see `README.md` and `wso2-hackathon-context-pack.md` for
the full stack tour).

---

## 1. Knowledge prerequisites — agentic AI concepts

You don't need to be an ML researcher, but you should be comfortable with the following concepts before
day one. If any of these are new, spend 30–60 minutes reading up before the event.

**LLM fundamentals**
- What an LLM is, what a prompt/completion/token is, and the idea of a context window.
- System prompts vs. user prompts, and basic prompt engineering (few-shot examples, structured
  instructions, output formatting).
- Temperature/sampling parameters and why they affect determinism.

**What makes an "agent" different from a chatbot**
- **Tool use / function calling** — an LLM deciding to call an external function/API and using the
  result to continue reasoning, rather than just generating text.
- **The agent loop** — plan → act (call a tool) → observe (tool result) → repeat until done.
- **Memory** — short-term (conversation history) vs. long-term (persisted facts/state across sessions).
- **RAG (Retrieval-Augmented Generation)** — retrieving relevant chunks from a knowledge base/vector DB
  and feeding them to the LLM so answers are grounded in real data instead of hallucinated.
- **MCP (Model Context Protocol)** — an emerging standard for exposing tools/data sources to an agent in
  a uniform way, so tools are discoverable and callable regardless of which LLM or framework you use.
- **Multi-agent orchestration** (optional, good to know) — multiple specialized agents coordinating on a
  task, vs. a single agent with many tools.
- **Guardrails** — PII masking, prompt-injection protection, content filtering, and rate/cost limiting
  applied to LLM traffic.
- **Observability & evaluation** — tracing what an agent did (which tools, in what order), and
  evaluating output quality (assertions or "LLM-as-judge").
- **Identity for agents** — the idea that an agent, not just a human user, can have its own verifiable
  identity and be authorized for specific actions.

**General technical baseline**
- Comfortable writing code in at least one language relevant to your stack (Python/Node.js typical for
  agent frameworks; Ballerina if using WSO2 Integrator).
- Basic REST API concepts (endpoints, auth headers, JSON payloads) — agents call APIs as tools constantly.
- Basic Git/GitHub workflow, since you'll likely collaborate on a shared repo.
- Comfortable with the command line (installing packages, running local servers, using Docker).

**Suggested pre-reading**
- Skim `wso2-hackathon-context-pack.md` in this repo once, even if you don't plan to use WSO2 — it has a
  concise mental model of how gateways, agents, RAG, identity, and observability fit together.
- If you're new to agent frameworks, skim the docs/quick-start of whichever one you plan to use
  (LangChain, LlamaIndex, or the WSO2 Integrator's agent framework).

---

## 2. Computer specifications

| Requirement | Minimum | Recommended |
|---|---|---|
| RAM | 8 GB | 16 GB+ (needed if running several local containers — e.g. gateway + Integrator + Identity Server) |
| Disk space | 20 GB free | 40 GB+ free (Docker images, IDE, dependencies add up fast) |
| OS | macOS, Windows 10/11, or Linux | Any of the same — WSO2 tooling and Docker support all three |
| CPU | Any 64-bit dual-core+ | Modern quad-core+ for smoother container performance |
| Admin/local install rights | Required | You must be able to install software and run Docker without corporate lockdowns |
| Internet access | Required | Stable connection — you'll be calling LLM APIs and pulling Docker images/dependencies live |
| Virtualization enabled | Required (for Docker) | Check BIOS/UEFI settings if Docker fails to start, especially on Windows |

If your laptop can't meet the "recommended" column, that's fine — just plan to use **fewer, lighter-weight
products** (e.g. skip local Kubernetes/OpenChoreo and Agent Manager, and lean on SaaS/cloud-hosted modes
instead of running everything locally).

---

## 3. Software & tools to install beforehand

Install and verify these **before** the event so you're not burning hackathon time on downloads:

| Tool | Why you need it | Notes |
|---|---|---|
| **Rancher Desktop** (recommended) | Runs the AI Gateway, Identity Server, Agent Manager dev container, vector DBs, etc. locally — free, cross-platform, and its built-in Kubernetes (k3s) can also cover the local-k8s need below | Verify with `docker run hello-world` (or `nerdctl run hello-world` if using containerd) |
| **Colima** *(macOS/Linux alternative)* | Lighter-weight container runtime if you prefer Colima over Rancher Desktop | Enable Kubernetes with `colima start --kubernetes`; no native Windows support |
| Docker Desktop *(fallback)* | Also works if you already have it installed/licensed | Verify with `docker run hello-world` |
| **Git** | Clone the starter kit, collaborate with your team | |
| **A code editor / IDE** | VS Code (or your preferred editor) for general coding | |
| **Java 17+ (JDK)** | Required by several WSO2 JVM-based runtimes | Verify with `java -version` |
| **Node.js 18+ and/or Python 3.10+** | Most agent frameworks (LangChain, LlamaIndex) and custom glue code run on one of these | Install whichever matches your team's stack |
| **curl / Postman (or similar API client)** | Testing REST/LLM/MCP endpoints directly, independent of your agent code | |
| **An LLM provider API key** | OpenAI, Anthropic, Azure OpenAI, Mistral, or AWS Bedrock — whichever provider you plan to use | Keep this key handy but plan to place it **behind** a gateway/proxy, not hard-coded in agent code. Need a **free** key? Mistral offers a free-tier API key (La Plateforme) — good option if you don't have a paid provider account yet |
| **WSO2 Integrator IDE** *(only if building agents/RAG in WSO2 Integrator)* | The IDE for the Ballerina-based agent framework, RAG, and Copilot scaffolding | Download: https://wso2.com/products/downloads/?product=wso2integrator |
| **A vector DB** *(only if doing RAG)* | Weaviate, Milvus, Pinecone, or pgvector — pick one supported by your framework | Can run as a Docker container locally |
| **kind/k3d** *(only if using OpenChoreo or Agent Manager)* | Local Kubernetes cluster for hosting/observability components | Skip if you don't plan to host on Kubernetes — Rancher Desktop's built-in k3s can also cover this instead of a separate tool |

**Quick verification checklist:**
```bash
docker --version && docker run hello-world
git --version
java -version
node --version    # or: python3 --version
curl --version
```

---

## 4. WSO2 products (optional — only if your idea uses them)

You do **not** need any WSO2 product to build an AI Agent at this hackathon — you're free to use any
stack. If you'd like to use the WSO2 stack, pick only the products your idea genuinely needs (2–4 is
typical; judges reward *meaningful* use, not the number of logos in your architecture). Full detail,
setup commands, and composition patterns are in `wso2-hackathon-context-pack.md` — read that before
touching any WSO2 config, since these products shipped/changed in 2026 and are newer than most AI coding
assistants' training data.

| Product | What it gives you | Reach for it when |
|---|---|---|
| **WSO2 API Platform** (AI Gateway) | One control point for API, LLM, and MCP traffic: LLM routing, guardrails, token limits; auto-generates MCP tools from your APIs | Your agent calls LLMs, or needs to call enterprise APIs safely as tools |
| **WSO2 Integration Platform** (WSO2 Integrator) | Low-code/pro-code agent building, RAG pipelines, memory, 600+ connectors, built-in evaluation | You are building the agent itself, RAG, or connecting to enterprise systems |
| **WSO2 Identity Platform** | Identity for users and, via **Agent ID**, for agents themselves, plus MCP authorization | Users log in/consent, or you want the agent to be a governed, authorizable identity |
| **Agent Manager Platform** (pre-GA) | Deploy, observe, evaluate, and secure agents at scale | You want to host, trace, and grade your agent — a bonus-points play |
| **OpenChoreo** | Kubernetes-native hosting | You want a credible production hosting story |
| **Moesif** | Analytics and monetization for AI/API traffic | You want to show AI cost/usage or a monetization angle |

**If you plan to use WSO2 products, do this before the event:**
1. Read `README.md` and paste `wso2-hackathon-context-pack.md` into your AI coding tool at the start of
   any session (and install the skill file if your tool supports skills/rules — see the README for
   per-tool install paths).
2. Decide which 2–4 products your idea needs (skim the composition patterns in the context pack).
3. Pre-install anything version-specific (Docker images, IDE) so you're not downloading during the event.
4. Get an LLM provider API key ready — you'll route it through the AI Gateway, not embed it in agent code.
5. If using Agent Manager or Identity Platform, note they may require signup/console access — set that up
   in advance rather than during the hackathon.

---

## 5. Final pre-event checklist

- [ ] I understand the agent loop, tool use, RAG, and MCP at a conceptual level
- [ ] My laptop meets the minimum specs above, and I have admin/install rights
- [ ] Docker, Git, an IDE, and my chosen language runtime (Node/Python/Java) are installed and verified
- [ ] I have an LLM provider API key ready
- [ ] I've skimmed `README.md` and `wso2-hackathon-context-pack.md` (even if not using WSO2)
- [ ] (If using WSO2) I've picked my 2–4 products and pre-installed anything version-specific
