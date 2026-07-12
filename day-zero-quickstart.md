# WSO2 Hackathon — Day-Zero Quick Start

> **Goal:** get *something WSO2 running on your machine* in the first hour, with the fewest possible
> blockers, so you spend the hackathon building and not fighting setup. This is a **menu, not a
> golden path** — do only the sections your idea needs.
>
> **Two ground rules that save you time:**
> 1. **Prefer local / standalone / on-prem** paths (no signup, no region limits). SaaS options are
>    noted where they're genuinely easier.
> 2. **Commands below were correct against mid-2026 releases** but are **version-pinned**. Version
>    numbers move. If a download 404s, go to the linked docs and grab the current version — don't
>    guess a URL.

---

## 0. Prerequisites (install these once)

- **Docker + Docker Compose** — the shipping vehicle for most WSO2 components.
- **Java 17+** — for JVM-based runtimes (Integrator profiles).
- **VS Code** — plus the **WSO2 Integrator** extension if you'll build agents/integrations.
- **Node.js 18+** and **Python 3.10+** — for your own agent/app code.
- **An LLM provider API key** (OpenAI/Anthropic/Azure/Mistral/Bedrock) — you'll put it *behind* the
  AI Gateway, not in your agent code.
- Give Docker enough resources (≥ 8 GB RAM recommended if running multiple components).

---

## 1. API Platform — standalone gateway (LLM + MCP governance), fully local

This is the **on-prem-friendly path**: no control-plane signup, configured by YAML/CLI. It's the
same Go-based gateway the cloud uses, just run independently. You can attach a control plane later.

**Get the current version and commands from:**
https://wso2.com/api-platform/docs/get-started/ (choose the *standalone gateway* / *AI gateway*
quick start) and https://github.com/wso2/api-platform (releases).

The launch-era download looked like the following — **check the releases page for the current
version tag before running**, since the version in the URL changes:

```bash
# Illustrative — confirm the current version tag on the releases page first.
curl -sLO https://github.com/wso2/api-platform/releases/download/gateway/v1.0.0/wso2apip-api-gateway-1.0.0.zip
unzip wso2apip-api-gateway-1.0.0.zip
cd wso2apip-api-gateway-1.0.0
```

You configure APIs, LLM providers, guardrails, and MCP tool generation via YAML/CLI/REST. For LLM
governance you point the LLM Proxy at your provider and give your agent the gateway's endpoint
instead of the provider's. For MCP, you feed it an OpenAPI spec and it derives tools.

> ⚠️ **Hybrid caveat.** The *hybrid* mode (standalone gateway + WSO2-hosted cloud control plane) was
> documented as **US-region only** at launch, with the hybrid control plane being Bijira SaaS. If
> you're in Africa and want the console UI, either use the SaaS control plane if region allows, or
> stay standalone and drive everything by YAML/CLI locally. Verify current region support in the docs.

> **To emit analytics to Moesif** (optional, Pattern F): the gateway config takes a `MOESIF_KEY`
> environment variable. Get the key from your Moesif account.

---

## 2. WSO2 Integrator — build the agent / RAG / integrations

The fastest dev loop is **VS Code + the WSO2 Integrator extension** (gives you the MI profile, agent
framework, RAG, and MI Copilot). Install steps and the "build your first AI integration" tutorial
(chatbot → knowledge base → RAG → agent) are here:

- Install & quick start: https://wso2.com/integration-platform/docs/
- First AI integration (start here for a RAG agent): https://mi.docs.wso2.com/en/latest/get-started/build-first-ai-integration/

For on-prem/container deployment of the runtime, follow the deployment section of the Integrator
docs (Docker/Kubernetes). For the hackathon, developing in VS Code and running the runtime locally is
the quickest.

> **Naming note:** docs may say "WSO2 Integrator: MI 4.6.0" — that's the MI *profile* of the unified
> WSO2 Integrator 5.0.0. Expected, not a mismatch.

---

## 3. Identity Platform — login + Agent ID (on-prem)

Runs cleanly on-prem. Download the Identity Server, start it, and configure an OAuth2/OIDC
application for your assistant's user login. For the agent-identity story, look specifically for
**Agent ID** and **MCP authorization** in the docs.

- Latest docs: https://is.docs.wso2.com/en/latest/

Add this only if user login/consent or agent-identity governance is part of your idea (Pattern D).

---

## 4. Agent Manager Platform — host / observe / eval (pre-GA)

Quick-start via a Docker dev container (version-pinned — **check the docs for the current tag**):

```bash
# Illustrative — confirm current version at the docs link before running.
docker run --rm -it --name amp-quick-start \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --network=host \
  ghcr.io/wso2/amp-quick-start:v0.18.0
# then, inside the container:
./install.sh
```

- Quick start: https://wso2.github.io/agent-manager/ (see Getting Started → Quick Start / on K3D)
- There's also a hosted console: https://console.agent-manager.cloud.wso2.com/

> ⚠️ **Pre-GA.** Expect the version and commands to change between now and the event. Auto-
> instrumentation targets LangChain/LlamaIndex — if you built your agent in one of those, this is
> your easiest observability win.

---

## 5. OpenChoreo — Kubernetes-native hosting (optional)

If you want a real hosting story or you're using Agent Manager (built on OpenChoreo), follow:
https://openchoreo.dev/docs/ . You'll want a local Kubernetes (k3d/kind/minikube) or a cluster.

---

## 6. Moesif — analytics (SaaS, optional)

Sign up, get your project's API key, and set it as `MOESIF_KEY` in the AI Gateway config (Section 1).
Dashboards populate as AI/API traffic flows. Docs: https://www.moesif.com/docs

---

## 7. Suggested first-hour sequence (adapt to your idea)

1. **Decide your 2–4 products** using `composition-patterns.md`. Don't install what you won't use.
2. **Stand up the AI Gateway (standalone)** — Section 1. Get one LLM call flowing *through the
   gateway*. (You now have Pattern A.)
3. **Build a minimal agent** in Integrator (Section 2) that calls the gateway, or wire your own
   LangChain/LlamaIndex app to the gateway endpoint.
4. **Add one real capability:** either RAG (Pattern C, in Integrator) or an MCP tool from a mock
   OpenAPI spec (Pattern B, via MCP Gateway). Pick the one your idea needs.
5. **Only then** consider identity, observability, analytics, hosting for bonus points.

---

## 8. When setup breaks

- **A download URL 404s** → the version moved. Go to the product's releases/docs page and use the
  current tag. Never hand-edit a version into a URL and hope.
- **A command flag "doesn't exist"** → your AI tool probably hallucinated it. Check the live docs.
- **Region/region-locked errors on cloud** → switch to the local/standalone path for that product.
- **Ask your AI tool to fetch the exact doc page** rather than recall commands from memory — these
  products are newer than its training data.

---

*Commands verified against mid-2026 WSO2 releases. Because versions move and some features are
pre-GA, always confirm the current version tag and command at the linked docs before running.*
