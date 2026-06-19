# UI_MANIFEST.md — Build manifest

> The bridge between the spec (`PRODUCT_SPEC.md` §4), the wireframes (`design/wireframes/`), and the code. Build to this. The written spec wins on any conflict.

## Design tokens
Canonical tokens live in `design/wireframes/_tokens.css` (dark "terminal" theme). **Mirror the token names** (not exact hex) into the Tailwind theme. Rules: mono font for all numbers/tickers/prices; status uses color **+ icon + text**; facts (deterministic) and model views (LLM) render in visually distinct zones; never blur/tease the user's own data.

## Routing & screens
| Route | Screen | Wireframe | Spec |
|---|---|---|---|
| `/login` | Auth (Discord/Google/email) | `13-login-auth`* | §2.1, §4.3 |
| `/` `/chat/:id` | Solo chat (landing → active) | `02`, `03` | §4.3.1–2 |
| `/markets` (toggle) | Markets takeover (solo-only) | `04` | §4.3.3 |
| `/group/:id` | Group chat (closed / open) | `05` | §4.3.4–5 |
| `/groups/new` | Captain create/configure | `12` | §3.1, §4.3 |
| `/join/:invite` | Member join gate | `11` | §3.1, §4.3 |
| `/plans` | Subscription plans | `14-subscription-plans`* | §4.5 |
| `/account` | Account (sub/broker/groups) | `15-account-page`* | §4.5, §2.1 |

Conceptual references (not routes): `00-ia-two-gravities`, `01-shell-four-states`, `06-group-call-to-consent-ticket`, `07-trade-option-analysis`, `08-gated-section-states`, `09-leakage-native-vs-pasted`, `10-formation-flow`.

\* `13–15` are specified and were prototyped; their standalone HTML wireframes are pending regeneration into `design/wireframes/`.

## Component inventory → states
**Shell / chrome:** `AppShell` (4 layout states) · `LeftRail` · `TopBar` (`ChatMarketsToggle`, `TierBadge`, user menu) · `CommandBar` (slash + NL) · `RoleBadge` (Captain/Co-Captain/Member/Observer) · `EntitlementGate` (server-enforced) · `BrokerLinkFlow`.

**Group (chat-only):** `GroupChatFeed` · `VoteCard` *(pending · passed · cancelled-expired · cancelled-drift · locked)* · `ActiveStack` (multiple concurrent) · `PanicBanner` (broadcast → close-tickets) · `FirstJoinEmptyState`.

**Personal / Cockpit:** **`ConsentTicket`** *(draft · computing · insufficient-funds · drift-blocked · holding-to-confirm · submitting · filled · partial · rejected · expired)* — the membrane, highest-priority · `PositionsTable` (equity + option; assigned/expired) · `BuyingPowerMeter` · `AllocationBar` · `OrdersPanel` (working/armed/pending).

**Analysis:** `TickerSearch` · `PriceChart` · `FactStrip` (deterministic) · `ModelViewStrip` (labeled, disclaimed) · `OptionsChain` (focused; level-gated rows) · `ResearchCard` (loading/partial/cached/error; Pin / →ticket / Chart) · `TicketBuilder`.

**Monetization:** **`GatedSection`** *(tier-locked · quota-exhausted · group-locked · healthy meter)* · `QuotaMeter` · `PlansPricing` · `UpgradeSheet` · `GroupTierSettings` (the multiplier upsell).

**Account/auth:** `AuthScreen` (Discord/Google/email) · `AccountPage` (Profile/Subscription/Brokerage/Groups/Security) · `UsageMeters` · `BillingPanel` (Stripe).


**Mobile / responsive:** `BottomNav` (Chat · Markets · Groups · Account) · `Drawer` (rail) · `BottomSheet` (hosts `ConsentTicket` / `VoteCard` / `ActiveStack` / pinned) · stacked Markets. Mobile is first-class, not an afterthought.

## Recommended build order
1. `AppShell` + `LeftRail` + `TopBar` + token theme (the four-state skeleton).
2. `AuthScreen` → land in fresh solo chat on Free.
3. Solo chat: `CommandBar` → `ResearchCard` (wired to `apps/tools`) → Pin/Summary right panel.
4. `PriceChart` + Markets takeover (`FactStrip`/`ModelViewStrip`/`OptionsChain`).
5. Tiers: `GatedSection` + `QuotaMeter` + `PlansPricing` + server-side `EntitlementGate`.
6. Groups: `GroupChatFeed` + `VoteCard` + `ActiveStack`; then formation (`12`), join gate (`11`).
7. **`ConsentTicket`** + Plane-B wiring (sizing, buying-power, audit) — the money path.
8. `AccountPage` + `BillingPanel`; `realtime` reconnect/replay polish.

## Non-negotiables (enforce in `.cursor/rules/compliance.md`)
- Never paywall own-money or safety views (`PositionsTable`, `BuyingPowerMeter`, `ConsentTicket`).
- A passed vote **fans out consent tickets** — it never auto-trades; `/panic` broadcasts, never liquidates.
- Entitlement resolves against **session context** server-side (solo = user tier; group = group tier); the trade loop fires only from a natively-run, entitlement-checked action.
- `ConsentTicket` quantity is computed at **tap time** against live buying power; show drift status + disclaimer.
- Every order/vote/consent transition writes the **append-only audit log** with the rule-set version in force.
- **Mobile-first:** the shell adapts to drawer / bottom-nav / bottom-sheets; consent and voting work on touch; PWA web push delivers calls, panics, and passed votes.
