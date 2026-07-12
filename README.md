# WSO2 Africa Hackathon — Developer Starter Kit

Build an **Enterprise AI Assistant powered by WSO2**. This kit gets you productive fast, even if
you've never touched a WSO2 product before, and is designed to work *with* AI coding tools (Claude,
Cursor, Codex, etc.) rather than against them.

## What's in here

| File | For | Use it to |
|---|---|---|
| **`wso2-hackathon-context-pack.md`** | You **and** your AI tool | The master primer. Paste into your AI tool first. Product mental model, exact naming, what each product does, how they compose, and an anti-hallucination protocol. **Start here.** |
| **`composition-patterns.md`** | You **and** your AI tool | Product-agnostic building blocks (A–G) showing how WSO2 products snap together, with an honest "delete-it test" for meaningful integration. No prescribed architecture, no fixed industry. |
| **`day-zero-quickstart.md`** | You | Get something running in the first hour. Local/on-prem-first, verified commands, version-pinned with "check the docs" guardrails. |
| **`wso2-ai-assistant.skill.md`** | Your AI tool | Install as a skill/rules file in Claude/Cursor/etc. so the model loads the correct WSO2 mental model and fetches docs instead of inventing APIs. |

## The 3-step start

1. **Paste `wso2-hackathon-context-pack.md`** into your AI coding tool. If your tool supports
   skills/rules files, also add `wso2-ai-assistant.skill.md`.
2. **Skim `composition-patterns.md`** and pick the 2–4 WSO2 products your idea actually needs.
3. **Follow `day-zero-quickstart.md`** to stand them up locally and get one LLM call flowing through
   the AI Gateway.

## The one thing to remember

Every WSO2 product here is **newer than your AI model's training data.** The model *will* confidently
invent WSO2 commands and config that don't exist. The whole kit is built to counter that: correct
names, version-gating, and "when unsure, fetch the docs." Trust the linked docs over any model's
memory — including the one you're using right now.

## Scoring reality check

Bonus points reward **meaningful** multi-product integration. For every WSO2 product in your design,
ask: *if I delete it, does my assistant still work?* If yes, it's decoration. Make it load-bearing or
drop it. Two products doing real work beats five bolted on.

---

*Product facts current to mid-2026 and verified against WSO2 documentation. These products move fast
and some are pre-GA — re-verify version-specific details against the live docs before you build.*
