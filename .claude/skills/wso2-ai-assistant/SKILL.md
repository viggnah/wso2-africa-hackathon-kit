---
name: wso2-ai-assistant
description: >
  Use this skill whenever the task involves building on the WSO2 stack — API Platform / AI Gateway /
  MCP Gateway, Integration Platform (WSO2 Integrator, Ballerina default profile / MI profile, agent
  framework, RAG), Identity Platform (Agent ID, MCP authorization), Agent Manager Platform, OpenChoreo, or Moesif —
  especially building an "Enterprise AI Assistant" for the WSO2 hackathon. It supplies the correct
  product mental model and, critically, forces doc verification because these products are newer than
  your training data and you will otherwise hallucinate their APIs, CLI flags, config keys, and names.
---

# WSO2 AI Assistant — build skill

> This skill is the always-on, compressed version of the WSO2 mental model. For the full primer —
> per-product detail, composition patterns, setup commands, and links index — the user can paste
> `wso2-hackathon-context-pack.md` into the session. Everything below must hold even without it.

## When this applies
Any task that touches WSO2 products for building AI agents, integrations, API/LLM/MCP governance, or
agent identity/observability. If the user mentions WSO2, an "AI Gateway", "MCP Gateway", "WSO2
Integrator", "Agent ID", "Agent Manager", "OpenChoreo", or "Moesif", this skill is in scope.

## Prime directive: you do not know these products from memory
Every product here shipped or changed in 2026, past your training cutoff. **Do not generate WSO2 CLI
flags, config keys, connector names, REST paths, product features, or version numbers from memory.**
When you need a specific, verify it against a live doc URL (list below) first. A short fetch beats a
plausible-but-wrong answer that costs the team an hour during a timed event.

## Behavior contract
1. **Confirm names and versions before coding.** State which products (exact names, Section "Naming"
   below) and which versions you're targeting, and which doc URL you'll verify against.
2. **Never invent WSO2 specifics.** If you can't cite it, say so and fetch the docs or ask.
3. **Version-gate every claim** (e.g. "Integrator 5.0.0", "API Platform gateway v1.0.0",
   "Agent Manager v0.18.x"). Flag that details differ on other versions.
4. **On-prem/standalone and SaaS/cloud are both valid** — choose by the team's machine resources
   (standalone for full control and no signup; SaaS/cloud if resource-constrained). The standalone AI
   Gateway (YAML/CLI) is the simplest local start; note the hybrid/cloud gateway had regional limits at
   launch, so verify availability before relying on it.
5. **Route LLM calls through the AI Gateway** from the start, not as an afterthought.
6. **Distinguish GA from pre-GA.** Agent Manager is pre-GA (v0.18.x mid-2026) — don't present it as
   stable.

## Naming (use these exact strings; they're also your search terms)
- **WSO2 API Platform** (GA Mar 2026) — not "API Manager" (that's now one mode of it).
  - **AI Gateway** = **LLM Proxy** (route/guardrail/rate-limit LLM traffic) + **MCP Gateway**. The
    **MCP Gateway** onboards MCP servers 3 ways: generate one from a REST API / OpenAPI definition
    (auto-derives tools), generate from an existing API, or **proxy an existing/remote MCP server**.
    ⚠️ **"MCP Proxy" = only that last mode** (fronting an existing MCP server) — do NOT call the
    OpenAPI→MCP generation "MCP Proxy". Go-based runtime; standalone or control-plane-connected.
- **WSO2 Integration Platform** → contains **WSO2 Integrator** (5.0.0 unified BI+MI+SI). **Default
  profile runs on Ballerina** (encouraged path, what the GenAI docs use); the **MI profile** still
  exists but is not the default — build AI/agent work against the GenAI docs, not MI-only docs. Agent
  framework + MCP + RAG + memory + built-in LLM evals + Agent Execution Visualizer + built-in Agent ID
  integration + 600+ connectors + Copilot.
- **WSO2 Identity Platform** — CIAM/IAM + **Agent ID** (agent identity) + **MCP authorization**.
- **Agent Manager Platform** — pre-GA; deploy/observe/eval agents on K8s; OTel; auto-instruments; built on OpenChoreo.
- **OpenChoreo** — OSS Kubernetes-native hosting/IDP.
- **Moesif** — SaaS AI/API analytics + monetization; fed by the AI Gateway (`MOESIF_KEY`).

## Composition guidance
Propose the **smallest stack that makes the idea real**, then a bonus-points version. Typical blocks:
LLM Gateway (cost/guardrails) · MCP Gateway (enterprise APIs → agent tools) · Integrator RAG/agent ·
Agent ID (agent + user identity) · Agent Manager (observe/eval) · Moesif (analytics) · OpenChoreo
(host). Apply the test: if a product can be deleted and the assistant still works, it's decoration —
make it load-bearing or drop it.

## Authoritative docs (fetch these; don't recall)
- API Platform: https://wso2.com/api-platform/docs/get-started/ · https://github.com/wso2/api-platform
- AI Gateway: https://wso2.com/api-platform/ai-gateway/
- Integration Platform / WSO2 Integrator: https://wso2.com/integration-platform/docs/ · GenAI overview
  (start here): https://wso2.com/integration-platform/docs/genai/overview · first AI integration tutorial:
  https://wso2.com/integration-platform/docs/genai/tutorials/it-helpdesk-chatbot
- Identity: https://wso2.com/identity-platform/docs/ · https://is.docs.wso2.com/en/latest/
- Agent Manager: https://wso2.github.io/agent-manager/ · https://github.com/wso2/agent-manager
- OpenChoreo: https://openchoreo.dev/docs/
- Moesif: https://www.moesif.com/docs

## Default first response when asked to build
Reply with: (a) the products + versions you'll target, (b) the doc URL(s) you'll verify against,
(c) the smallest viable stack and a bonus-points variant, (d) an explicit note of anything you need
to fetch before writing config. Then proceed — fetching docs as needed — without inventing specifics.
