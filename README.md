# Andrey Bolshakov

AI product engineer in Limassol, Cyprus. Former CTO, still writing the code.
I build LLM products end to end — retrieval, agents, guardrails, evals, cost — and ship them to production for real users.
I work AI-native: coding agents are part of my daily toolchain.

## What I'm building

- **[AnroAgents](https://anroagents.com)** — platform to build and embed AI agents on business websites.
  RAG over customer content, a 9 KB embeddable widget with proactive triggers, WordPress and Shopify integrations,
  an A2A endpoint and its own ARD agent registry so other AI assistants can find customers' agents.
  Open source: **[anroagents-mcp](https://github.com/Anrotech/anroagents-mcp)** — MCP server
  ([`@anroagents/mcp-server`](https://www.npmjs.com/package/@anroagents/mcp-server)) to create and manage agents from Claude and other MCP clients.
- **[WeavUp](https://weavup.app)** — AI networking agent for Telegram and MAX.
  Go on AWS Lambda + SQS, pgvector matching, LLM agents that search, introduce and negotiate,
  with validators against hallucinated promises and quote-grounded extraction.
- **[Angel](https://angelai.ws)** — modular AI life assistant for Telegram and MAX.
  Go on AWS Lambda, a tool-use agent with model fallbacks, 13 opt-in modules the user switches on by talking to the bot;
  reminders moved from LLM-written text to structured events rendered by code.
- **EdTech course platform** — built from scratch as a solo engineer: AI homework review (multimodal, JSON mode),
  RAG support knowledge base with streaming chat, messaging pipelines across two messengers.

**Also shipped for clients:**
[Explorentia](https://explorentia.eu) — QR-based village tours for Cyprus tourism, live in the client's own AWS account (Next.js, Hono on Lambda, Neon) ·
[Root + Reach](https://rootreachyoga.com) — multi-tenant SaaS for yoga teachers with live video classes (LiveKit) and Stripe Connect payouts.

## Case studies

- [An AI product analyst embedded in a client team](case-studies/ai-product-analyst.md) — a Telegram agent that answers product questions from read-only code and production data, prices changes and triages errors, with a human approving every deploy.
- [Three evidence-based decisions on an AI agent platform](case-studies/anroagents-decisions.md) — choosing the model on the real pipeline, pricing so no plan loses money, and shutting down a distribution channel nobody used.

## Stack

**AI:** RAG (pgvector), agents and tool use, MCP, A2A, structured extraction, multimodal, LLM cost tracking ·
Claude, Mistral, DeepSeek, OpenAI, xAI Grok, OpenRouter
**Backend:** Go, PHP / Laravel, Node.js, Python · PostgreSQL (Neon), DynamoDB, Redis, SQS
**Frontend:** React, Vue 3, TypeScript, Tailwind, Telegram mini-apps
**Cloud:** AWS (Lambda, SAM, S3, CloudFront, Cognito), GitHub Actions, Sentry

## Elsewhere

[LinkedIn](https://www.linkedin.com/in/andrey-v-bolshakov) · [ANRO Technologies](https://anroit.com)
