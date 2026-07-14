# WSO2 Africa Hackathon — Developer Starter Kit

Build an **Enterprise AI Assistant powered by WSO2** that solves a real problem for an industry you
choose. It should securely connect to enterprise systems, invoke APIs, automate workflows, and provide
intelligent, actionable experiences. Pick any industry and compose the products however you like —
**there is no prescribed architecture.**

This kit gets you productive fast, even if you've never touched a WSO2 product, and is designed to work
*with* AI coding tools (Claude, Cursor, Codex, etc.). Read this page once, then hand the context pack to
your AI tool and start building.

## What's in this kit

| File | For | Use it to |
|---|---|---|
| **This README** | You | The map and human-readable overview: product tour, how the pieces fit, first-hour setup, what judges reward. **Start here.** |
| **`wso2-hackathon-context-pack.md`** | You **and** your AI tool | The master primer. **Paste it into your AI tool first.** Mental model, exact naming, per-product detail, 7 composition patterns, setup commands, links index, and an anti-hallucination protocol. |
| **`wso2-ai-assistant.skill.md`** | Your AI tool | Install as a skill/rules file (Claude/Cursor/Codex/etc.) so the model loads the correct WSO2 mental model and fetches docs instead of inventing APIs. |

## A stack for the agentic enterprise

WSO2 gives you products that together let you build, govern, run, and observe AI agents and the systems
they talk to. **You do not need all of them.** A strong submission might use two; an ambitious one might
use five. What matters is that each product you include does real work.

| Product | What it gives you | Reach for it when |
|---|---|---|
| **WSO2 API Platform** (AI Gateway) | One control point for API, LLM, and MCP traffic: LLM routing, guardrails, token limits; and auto-generating MCP tools from your APIs. | Your assistant calls LLMs, or needs to call enterprise APIs safely as agent tools. |
| **WSO2 Integration Platform** (WSO2 Integrator) | Low-code and pro-code building of agents, RAG pipelines, memory, and integrations, with 600+ connectors and built-in evaluation. | You are building the agent itself, RAG, or connecting to enterprise systems. |
| **WSO2 Identity Platform** | Identity for users and, via **Agent ID**, for the agents themselves, plus MCP authorization. | Users log in or consent, or you want the agent to be a governed, authorizable identity. |
| **Agent Manager Platform** (pre-GA) | Deploy, observe, evaluate, and secure agents at scale, with zero-code instrumentation for LangChain and LlamaIndex. | You want to host, trace, and grade your agent — a strong bonus-points play. |
| **OpenChoreo** | Open-source, Kubernetes-native hosting for your components. | You want a credible production hosting story. |
| **Moesif** | SaaS analytics and monetization for AI and API traffic, fed by the AI Gateway. | You want to show AI cost and usage, or a monetization angle. |

**A note on names.** These products were released or renamed in 2026. Use the exact names above — e.g.
what used to be "API Manager" is now one deployment mode of the **WSO2 API Platform**, and the BI and MI
products are now unified as **WSO2 Integrator** (whose **default profile runs on Ballerina**). The context
pack has a full naming table; getting names right also makes your docs searches work.

## How the products fit together

Think of it as a chain. A user (authenticated by the **Identity Platform**) talks to your assistant. The
assistant — built in **WSO2 Integrator**, or in your own framework — is the brain. When it needs to
*think*, it calls an LLM through the **AI Gateway's LLM Proxy**, which adds cost control and guardrails.
When it needs to *act*, it calls your enterprise APIs as tools through the **MCP Gateway**, which adds
authentication and an audit trail. **Agent Manager** watches and grades the agent; **Moesif** reports on
cost and usage; **OpenChoreo** hosts the whole thing.

The context pack breaks this into **seven reusable building blocks (A–G)** with an ASCII diagram. There is
deliberately **no single reference architecture and no worked industry example** — the room is yours.

## Working with AI coding tools

Most teams will build with AI assistants, and there's a catch: **every WSO2 product here is newer than the
training data of every current AI model.** Left alone, your model will confidently invent WSO2 commands,
config keys, and product names that don't exist. To counter that: **paste `wso2-hackathon-context-pack.md`**
into your tool at the start of a session, and if it supports skills/rules files, also install
**`wso2-ai-assistant.skill.md`**. Both carry one rule — *when unsure about a WSO2 specific, fetch the docs
rather than invent.* Trust the linked docs over any model's memory, including the one you're using now.

## The 3-step start

1. **Paste `wso2-hackathon-context-pack.md`** into your AI tool (and add the skill file if supported).
2. **Skim the composition patterns** (inside the pack) and pick the 2–4 products your idea actually needs.
3. **Follow the setup below** to stand them up and get one LLM call flowing through the AI Gateway.

## Setup — the first hour

A **menu, not a golden path** — do only what your idea needs. **On-prem/standalone and SaaS/cloud both
work; pick by your machine's resources.** Commands are pinned to mid-2026 releases — if a URL 404s, get the
current version from the docs, don't guess. (The same commands live in the context pack so your AI tool can
run them with you.)

**Prerequisites:** Docker (+ ≥ 8 GB RAM), Java 17+, WSO2 Integrator IDE (if building agents), Node 18+ /
Python 3.10+, and an LLM provider key (OpenAI/Anthropic/Azure/Mistral/Bedrock) — it goes *behind* the
gateway, not in your agent code.

**1. AI Gateway (API Platform) — LLM + MCP governance.** Standalone (YAML/CLI, simplest local) or
control-plane-connected (web UI). Confirm the current version on the
[releases page](https://github.com/wso2/api-platform) and follow
[get-started](https://wso2.com/api-platform/docs/get-started/) (*standalone AI gateway* quick start):

```bash
# Illustrative — confirm the current version tag on the releases page first.
curl -sLO https://github.com/wso2/api-platform/releases/download/gateway/v1.0.0/wso2apip-api-gateway-1.0.0.zip
unzip wso2apip-api-gateway-1.0.0.zip && cd wso2apip-api-gateway-1.0.0
```

Point the LLM Proxy at your provider and give your agent the gateway's endpoint. Feed MCP an OpenAPI spec
and it derives tools. Set `MOESIF_KEY` in the config to emit analytics to Moesif.

**2. WSO2 Integrator — agent / RAG / integrations.** Use the WSO2 Integrator IDE on the **Ballerina default
profile**, and start from the GenAI docs (not MI-only docs):
[docs home](https://wso2.com/integration-platform/docs/) ·
[GenAI overview](https://wso2.com/integration-platform/docs/genai/overview) ·
[first AI integration tutorial](https://wso2.com/integration-platform/docs/genai/tutorials/it-helpdesk-chatbot).

**3. Identity Platform — login + Agent ID.** On-prem (self-managed Identity Server) or SaaS. Configure an
OAuth2/OIDC app for login; look for **Agent ID** and **MCP authorization**. Add only if identity is part of
your idea. [Platform docs](https://wso2.com/identity-platform/docs/) ·
[IS docs](https://is.docs.wso2.com/en/latest/).

**4. Agent Manager Platform (pre-GA) — host / observe / eval.** Docker dev container (check the docs for the
current tag):

```bash
# Illustrative — confirm current version at the docs link before running.
docker run --rm -it --name amp-quick-start \
  -v /var/run/docker.sock:/var/run/docker.sock --network=host \
  ghcr.io/wso2/amp-quick-start:v0.18.0
# then, inside the container: ./install.sh
```

[Quick start](https://wso2.github.io/agent-manager/) ·
[hosted console](https://console.agent-manager.cloud.wso2.com/). ⚠️ Pre-GA — expect changes.

**5. OpenChoreo (optional) — K8s hosting.** [Docs](https://openchoreo.dev/docs/). Needs a local Kubernetes
(k3d/kind/minikube) or a cluster.

**6. Moesif (optional, SaaS) — analytics.** Sign up, set `MOESIF_KEY` in the gateway config (step 1).
[Docs](https://www.moesif.com/docs).

**First-hour sequence:** decide your 2–4 products → stand up the gateway and get one LLM call flowing
*through* it → build a minimal agent (in Integrator, or wire your own LangChain/LlamaIndex to the gateway)
→ add one real capability (RAG or an MCP tool from a mock OpenAPI spec) → only then identity, observability,
analytics, hosting.

**When it breaks:** a 404 means the version moved (use the current tag, never hand-edit a URL); a flag that
"doesn't exist" is probably a hallucination (check the live docs); ask your AI tool to fetch the exact doc
page rather than recall from memory.

## What the judges reward

Bonus points go to projects that integrate multiple WSO2 products **meaningfully.** Apply one test: *for
every WSO2 product in your architecture, would your assistant still work if you removed it?* If yes, that
product is decoration — make it load-bearing or take it out. **Two products doing real work beat five bolted
on for show.**

---

*Product facts are current to mid-2026 and verified against WSO2 documentation. These products evolve
quickly and some are pre-GA — always confirm version-specific details against the live docs before building.*
