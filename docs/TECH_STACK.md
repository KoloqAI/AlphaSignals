# TECH_STACK.md — Technology choices

> Phase-1 stack with rationale. Choices follow the spec (§5–§6) and the library posture from review. Names are decisions; versions are starting points.

## Frontend (`apps/web`, `packages/ui`)
- **Next.js 15 (App Router)** + **TypeScript** — the shell and all screens.
- **Tailwind CSS** + **shadcn/ui** — components; design tokens mirrored from `design/wireframes/_tokens.css`.
- **TradingView Lightweight Charts** (Apache-2.0, attribution required) — price/candles; matches the terminal aesthetic.
- **Tabler Icons** — iconography (used throughout the wireframes).
- **Vercel AI SDK + AI Elements** — streaming chat UI and structured tool-call blocks.

## Intent & orchestration (`apps/api`)
- **Command dispatcher** — slash-commands route deterministically; free-form NL goes through a lightweight LLM that *translates to a command*. One pipeline serves web now and the Discord bot later.
- **LiteLLM gateway** over a managed provider — abstracts models so tiers map to model power (**fast → strong → frontier**) and versions update without code changes.
- **zod** — every model output that becomes structured data (intent, suggested ticket) is schema-validated before use. Money intent prefers explicit commands over NL.

## Research tools & agents (`apps/tools`, Python/FastAPI)
- **pandas-ta** (MIT) — technical indicators (deterministic; the numbers are identical across tiers).
- **py_vollib / vollib** — Black-Scholes greeks & IV for single-leg options.
- **FinRobot** (Apache-2.0) — deep equity-research generation; **evidence only**, wrapped with timeouts + graceful degradation.
- **TradingAgents** (Apache-2.0) — bull/bear analyst debate surfaced as **evidence, never a verdict or an order**.
- Data providers (fundamentals/news/sentiment) behind typed adapters; sentiment is labeled as a derived, non-deterministic signal.
> These libraries are Python, which is why `apps/tools` is a Python service rather than reimplemented in JS. The Node orchestrator calls it as a typed tool API.

## Execution & consensus (`apps/execution`, `packages/broker`) — Plane B
- **TypeScript** deterministic state machines (orders, proposals); pure sizing/tally/risk functions.
- **`BrokerAdapter` interface** with an **Alpaca** implementation (v1). **SnapTrade** is a Phase-2 adapter (gated on its commercial trading agreement).
- **Alpaca SDK** — order placement, positions/buying-power (source of truth), market data (real-time gated by tier; delayed for Free).
- Holds the only broker credentials in the system (capability isolation).

## Real-time (`apps/realtime`)
- **Socket.io** to start; **Centrifugo** when fan-out scales (broadcast-to-many is the native workload).
- **Authoritative event channel**: Postgres **outbox** → fan-out; ordered, acked, replayable on reconnect. Financial state never rides the LLM token stream. (Redpanda/Kafka optional later.)

## Data & persistence
- **PostgreSQL** — primary ledger; **append-only** audit log + outbox (compliance spine).
- Caching layer (Redis) for `/research` snapshots (per-ticker TTL, shared across a room) and rate-limit counters.

## Auth & payments
- **NextAuth** — OAuth with **Discord** + **Google**, plus email magic-link. Discord grant also seeds the future broadcast/deep-link connector.
- **Stripe** subscriptions — Free / Pro / Premium. *(Stripe Connect / Captain payouts are deferred: the federated/influencer model is out of Phase 1; everyone simply subscribes at their tier.)*

## Library posture — use / reference / avoid
| Library | License | Posture |
|---|---|---|
| Lightweight Charts, pandas-ta, py_vollib, Vercel AI SDK, zod, Socket.io/Centrifugo, Alpaca | permissive / commercial-incl. | **Use** |
| FinRobot | Apache-2.0 | **Use** (Plane A, wrapped, evidence-only) |
| TradingAgents | Apache-2.0 | **Reference / evidence-only** — authors flag it research-only & non-deterministic; never let it decide/execute |
| OpenBB Platform | **AGPL-3.0** | **Internal/prototype only** — network-service use forces source-disclosure or a commercial license |
| FinRL family | OSS | **Avoid (v1)** — RL trading = automated discretion, wrong plane |
| MlFinLab | **proprietary** | **Avoid unless licensed** — not open source; commercial use forbidden without a paid license |
| yfinance | — | **Prototype only** — TOS forbids commercial redistribution |

> Add a one-time dependency **license audit** scoped to everything in the data and order paths as a release gate.

## Deferred to later phases
SnapTrade (multi-broker), multi-leg options, a custom market-data trigger engine, backtesting, time-series prediction in the platform's voice, Stripe Connect / payouts, the Discord bot surface (connector only at first).
