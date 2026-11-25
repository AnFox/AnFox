# Case study: three evidence-based decisions on an AI agent platform

Andrey Bolshakov · November 2026

## Context

[AnroAgents](https://anroagents.com) lets a business create an AI agent from its website in minutes and put it on the site, in WordPress or Shopify, or behind MCP and A2A. I built it end to end and own the product, engineering and pricing decisions.

The platform is early: the product is live, revenue is small. At this stage the expensive mistakes are not bugs but bets — a model that sounds wrong to prospects, a plan that loses money on its best customer, a distribution channel that nobody uses. Below are three such calls, each made on measurements rather than on a hunch.

| Area | Stack |
| --- | --- |
| Frontend | React 19, Vite, Tailwind, shadcn/ui |
| Backend | AWS SAM, Lambda, API Gateway, DynamoDB, SQS |
| Retrieval | Neon Postgres + pgvector |
| Models | Claude Haiku (default), Claude Sonnet (opt-in) |
| Widget | Preact + Shadow DOM, 9 KB gzip |
| Billing | Revolut subscriptions, crypto via NOWPayments |

## Decision 1: pick the model on the real pipeline, not on a benchmark

All chat traffic moved from gpt-4o-mini to Claude Haiku in October 2026, at the same cost of about $0.003 per turn, because on the real pipeline it fixed what prospects actually complained about.

**The problem.** Demo agents sounded clumsy. In real conversations they mixed up the reply language, invented details about the business, and ended answers with “write to info@” instead of moving the visitor to the next step.

**How it was tested.** Seven models went through one run of the production pipeline — the real prompt, retrieval over the customer's catalog and the answer guard that checks replies — on the same scenarios. The run swapped the model by overriding the config read inside the script, so the global setting never changed and live traffic was untouched.

**Result.** Haiku held the language, quoted the catalog accurately and led to the next step on its own. The provider is now one config record, so a rollback is one write.

**What switching cost.** The new model family needed changes the benchmarks do not mention: no `temperature` parameter (an `effort` setting instead), reasoning that eats into `max_tokens`, reasoning turned off for internal calls so summaries fit a 20-second limit, and photos in the history described in words.

## Decision 2: no plan is allowed to lose money

Pricing was rebuilt from the cost of one chat turn up, so that even the heaviest customer on any plan stays profitable after the 20% partner share.

**The problem.** The unlimited Business plan paid for itself only up to about 65,000 messages per account. One busy reseller studio could have turned it into a loss.

| Lever | Before | After |
| --- | --- | --- |
| Business plan | Unlimited messages | 50,000 per month per account, pooled across its agents; 5 € per 1,000 above |
| Overage, Starter | 5 € per 1,000 | 7 € per 1,000 |
| Overage, Pro | 4 € per 1,000 | 6 € per 1,000 |
| Advanced model (Sonnet) | — | Opt-in per agent; each reply counts as 15 messages |

**Why ×15 for the advanced model.** A Sonnet turn costs about $0.04 against $0.003 for Haiku. At ×10 a Business account running fully on Sonnet would lose money; at ×15 it does not (about 123 € of model cost against a 199 € plan). The multiplier keeps one unit customers already understand — messages — with no new price line or meter. Only the reply is multiplied; internal calls such as the answer check and summaries stay on Haiku, and a failed Sonnet reply is never charged ×15.

**Guards before the model.** A pool limits what can be billed, not how fast a script can burn it. Throttling runs before metering and before the model call — 20 messages per 5 minutes and 150 per day per visitor, 600 per hour per agent — so a rejected message costs the owner nothing. Visitors are keyed by browser id and by IP hash, since the id can be faked.

**Watching it.** Model cost is tracked per account per month, and a daily check alerts when it passes 50% of that account's revenue. Trials and promos count as non-paying, so loss-making accounts cannot hide behind them.

The same arithmetic cut the other way: embedding a million characters costs about $0.005, so knowledge-base limits were raised above every competitor reviewed (12 of them). Margin is bounded by messages, not by how much an agent knows.

## Decision 3: shut down a distribution channel nobody used

In August 2026 I retired our node in the AGNTCY agent directory and replaced it with our own ARD registry, because the measurements showed the node delivered nothing.

**The bet.** The product promises that when a buyer asks their AI assistant to find a supplier, the assistant finds our customer's agent. The AGNTCY node was meant to make that happen.

**What the data said.** In its whole life the node's records were never fetched from outside: zero pulls, zero views, zero exports. AGNTCY has no public storefront, and its API sits behind mutual TLS, so no one could federate with it. The node also cost 60–90 $ a month, and its database turned out to be the most expensive in the account: 416 hours of active compute in a billing period against 5 for the main product database, because a constant connection never let it sleep.

**What replaced it.** ARD treats agent discovery like search: a manifest on the publisher's domain plus registries that answer natural-language queries and federate over open HTTP. We declared anroagents.com a registry, so every customer's agent appears in other registries' results with no action from the customer. Ranking reuses the pgvector stack that already powers retrieval, and it embeds the questions buyers are likely to ask, not marketing copy.

**The reframe.** Publishing is free and anyone can do it, so the value moved to how well a business is described. The record is now built automatically at publish time: the model writes search phrases and service areas, everything else is derived from data the customer already entered. Every new field must be something buyers filter on or something the ranking reads.

**Shutting it down safely.** The order mattered: code was decoupled from the node before the stack was deleted, otherwise the first expired subscription would have hit two 60-second timeouts per agent and landed in the dead-letter queue. The data was archived before deletion, and the signing key was kept to sign the new trust manifests.

## What carries over

The three decisions share four habits I bring to any AI product.

- **Test on the production path.** Model choice was made on the real prompt, retrieval and guard, with live traffic untouched — not on a leaderboard.
- **Price from the cost of one turn.** Every limit, multiplier and overage rate traces back to cents per turn and the worst-case customer.
- **Kill what the numbers say is dead.** A channel with zero pulls and the account's biggest database bill went, even though running it had earned us an invitation to shape the ecosystem's trust model.
- **Make changes reversible and cheap to roll back.** The model is one config record; the node was decoupled before deletion and its data archived first.
