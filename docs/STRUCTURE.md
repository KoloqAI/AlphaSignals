# STRUCTURE.md — Repository layout

> How the codebase is organized. The structure encodes the **two-plane architecture** (§5): the research plane (Plane A) and the execution plane (Plane B) are separate packages/services, and **only the execution service holds broker credentials** (capability isolation). The `schemas` package is the typed contract between them.

## Monorepo tree

```
alpha-signals/
├─ apps/
│  ├─ web/                 # Next.js 15 (App Router) — the shell, all screens, UI
│  │  ├─ app/              # routes (see UI_MANIFEST.md → Routing)
│  │  ├─ components/       # screen-level compositions (import from packages/ui)
│  │  └─ lib/              # client hooks, websocket client, command-bar client
│  ├─ api/                 # Node service — command dispatcher + Plane-A orchestration
│  │  ├─ dispatcher/       # slash-command + NL → command bus (one pipeline, both surfaces)
│  │  ├─ orchestrator/     # lightweight routing LLM; calls research tools/agents
│  │  └─ entitlements/     # server-side tier/quota/contextual-entitlement checks
│  ├─ execution/           # ⚠ PLANE B — execution & consensus engine (ISOLATED)
│  │  ├─ state-machine/    # order + proposal state machines (pure, deterministic)
│  │  ├─ consensus/        # server-arbitrated tally, idempotent votes, drift guard
│  │  ├─ sizing/           # % -of-committed sizing, buying-power gate (pure fns)
│  │  └─ audit/            # append-only audit log writer
│  ├─ tools/               # 🐍 Python (FastAPI) — deterministic research tools + agents
│  │  ├─ ta/               # pandas-ta indicators
│  │  ├─ options/          # py_vollib greeks / IV
│  │  ├─ fundamentals/     # data-provider adapters
│  │  └─ agents/           # FinRobot, TradingAgents (evidence only — NO verdicts)
│  ├─ realtime/            # websocket / fan-out (Socket.io now; Centrifugo at scale)
│  └─ worker/              # cron/jobs: broker reconciliation, quota resets, alerts
├─ packages/
│  ├─ ui/                  # design system: tokens, shadcn/ui, shared components (§4.6)
│  ├─ schemas/             # zod schemas — commands, orders, proposals, events, rule sets
│  │                       #   THE CONTRACT between Plane A (suggests) and Plane B (executes)
│  ├─ broker/              # BrokerAdapter interface + Alpaca impl (imported ONLY by execution)
│  └─ config/              # tier definitions, entitlement matrix, rule-set defaults
├─ docs/
│  ├─ PRODUCT_SPEC.md      # source of truth (§0–§6)
│  ├─ STRUCTURE.md         # this file
│  ├─ TECH_STACK.md
│  └─ UI_MANIFEST.md
├─ design/
│  └─ wireframes/          # *.html + _tokens.css + index.html (visual reference)
└─ .cursor/rules/
   ├─ ui.md                # treat PRODUCT_SPEC §4 as authoritative; wireframes as target
   ├─ architecture.md      # enforce the plane boundary + capability isolation
   └─ compliance.md        # never paywall own-money/safety; no auto-execute; consent-only
```

## Boundaries that must hold

- **Capability isolation.** `packages/broker` (broker credentials, order placement) is importable **only** by `apps/execution`. `apps/api`, `apps/tools`, and the agents have **no** trade scope — they can suggest a ticket, never place one. Enforce via package boundaries + lint rules + separate runtime secrets.
- **One-way contract.** Plane A emits a **suggested ticket** (a `schemas` object). The only thing that crosses into `apps/execution` is an explicit, authenticated **consent event** referencing it. No LLM runs inside `apps/execution`.
- **Determinism lives in code.** Sizing, tally, buying-power, risk caps are pure functions in `apps/execution` — never an LLM. Model outputs are confined to `apps/tools` + `apps/api/orchestrator`.
- **Authoritative events** (`proposal.* / vote.* / order.* / fill.*`) originate in `apps/execution`, persist to the Postgres outbox, and fan out via `apps/realtime`. Best-effort LLM token streams ride a separate channel.

## Why Python `tools` is its own app
`pandas-ta`, `py_vollib`, FinRobot, and TradingAgents are Python. Rather than reimplement, run a small FastAPI service that exposes them as typed tools to the Node orchestrator. Keeps the deterministic *numbers* in battle-tested libraries and isolates the heavy agent runtime. (See TECH_STACK.md.)
