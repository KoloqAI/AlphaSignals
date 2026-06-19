# Cursor rule: architecture / planes (always apply)

- Two planes (§5). **Plane A** (research, LLM, non-deterministic) and **Plane B** (execution, money, deterministic) are separate. Respect the package boundaries in `docs/STRUCTURE.md`.
- **Capability isolation:** `packages/broker` (credentials, order placement) is importable **only** by `apps/execution`. `apps/api`, `apps/tools`, and agents have **no trade scope** — they may *suggest* a ticket, never place one.
- **One-way contract:** Plane A emits a `schemas` suggested-ticket; the only thing crossing into `apps/execution` is an explicit, authenticated **consent event**. No LLM runs in `apps/execution`.
- **Determinism = code:** sizing, vote tally, buying-power, risk caps are pure functions in `apps/execution`. Never an LLM. "Deterministic" never means a low-temperature model.
- **Authoritative events** (`proposal.* / vote.* / order.* / fill.*`) originate in `apps/execution` → Postgres outbox → `apps/realtime` fan-out (ordered, acked, replayable). Financial state never rides the LLM token stream.
- **Dispatcher:** slash-commands route deterministically; free-form NL is translated to a command then validated with zod. Money intent prefers explicit commands.
- **Broker:** access only via the `BrokerAdapter` interface. Alpaca is the v1 impl; SnapTrade is a Phase-2 adapter, not a rewrite.
- Deterministic research tools (pandas-ta, py_vollib) and agents (FinRobot, TradingAgents) live in the Python `apps/tools` service; they return typed data and never place orders.