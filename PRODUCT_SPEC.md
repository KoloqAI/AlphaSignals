# Alpha-Signals.ai: Product Requirements & Specification Document (PRSD)

**Version:** 1.5 (Production Ready)
**Status:** Architecture Locked
**Goal:** A collaborative, high-performance, AI-driven trading workspace for retail syndicate groups.

---

## 1. Core Architecture & System Topology

Alpha-Signals.ai utilizes an asymmetrical Router-Agent middleware topography to balance sub-100ms inference routing with institutional financial safety parameters.

* LAYER 1: THE CLASSIFIER JUDGE (Light Inference: Qwen-8B / Gemma-2B)
  - Evaluates input intent via token evaluation in < 50ms processing speed.
  - Emits structured JSON payloads to determine processing destination.

* LAYER 2: ROUTING DESTINATIONS
  - [DETERMINISTIC] -> Native SQL Ops (Price Monitoring, Direct DB Alerts)
  - [AGENTIC] -> FinRobot Execution (Deep Multi-Agent Equity Sheets, Sentiment Engine)
  - [TRADING] -> Dynamic Governance Pipeline (Pre-Flight Balance Sweep, Generative UI Card Hydration)

* Frontend Core: Next.js 15 (App Router), TypeScript, Tailwind CSS, Shadcn/ui.
* AI UI Core: Vercel AI SDK + AI Elements (for optimized message streaming, structural tool-call blocks, and real-time reasoning rendering).
* LLM Multi-Model Gateway: LiteLLM Proxy engine deployed over AWS Bedrock (Claude 3.5 Sonnet orchestration layer / Llama 3 infrastructure).
* Backend Service Layer: Node.js execution runtime, WebSocket session layer (Socket.io), PostgreSQL main ledger database.

---

## 2. Module Specifications

### 2.1 Authentication & Financial Access Control
* Authentication Pipeline: OAuth2 integration managed via NextAuth, utilizing Discord/Google provider strings to capture session profiles, names, and avatar image assets.
* Core Account Session Machine:
  UNCONNECTED ===> AUTHENTICATED ===> STRIPE_ACTIVE ===> BROKER_TUNNELED
* The Squad Firewall (Broker Isolation): To completely bypass joint-venture legal liability and eliminate pool accounting risks, users maintain independent custody of their capital. Broker connections (Alpaca Broker API / Robinhood OAuth) are tokenized and encrypted at rest using AES-256-GCM.
* Virtual Allocation Cap Guardrail: Upon state entry to BROKER_TUNNELED, users must define an explicit capital boundary (e.g., $2,000). The internal execution engine is programmatically blinded to all available account equity outside this declared cap.

### 2.2 Hybrid Execution Engine (The "Classifier Judge")
All inputs entered into the workspace stream directly to a lightweight classification LLM model. This layer acts as a strict state traffic controller to prevent unnecessary token burn and protect operating margins.

Expected Judge Output Schema Pattern:
{
  "prompt": "/propose Buy 5% allocation of NVDA at $125 limit",
  "intent": "TRADING",
  "ticker": "NVDA",
  "confidence_score": 0.99,
  "routing_destination": "TRADING_ENGINE_PIPELINE",
  "pre_flight_check_required": true
}

| Intent Type | Architectural Destination | Processing Action | Compute Overhead |
| :--- | :--- | :--- | :--- |
| **DETERMINISTIC** | Direct Database Operations | Bypasses core LLM logic. Fires standard Python script lookups, raw cron routines, or numeric price alerts. | $0.00 |
| **AGENTIC** | FinRobot & TradingAgents Loop | Initializes multi-agent framework. Invokes Claude 3.5 Sonnet to construct deep equity sheets or historical strategy backtests. | Moderate |
| **TRADING** | Shared Room Governance Logic | Freezes standard text responses. Runs a pre-flight wallet validation check and hydrates an interactive voting widget. | Low |

### 2.3 Role-Based Access Control (RBAC) Matrix
Syndicate workspace features adapt dynamically based on the active participant's structural role profile token:

| Role Name | Platform Title | Voting Power | Can Trigger /propose | Can Request AI Analysis | Can Pin Content to Panels | Can Mutate Room Rules |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **Strategist** | Room Owner/Admin | Yes | Yes | Yes | Yes | Yes |
| **Signal Provider** | Room Co-Admin | Yes | Yes | Yes | Yes | No |
| **Validator** | Standard Member | Yes | No | Yes | Yes | No |
| **Observer** | Guest Account | No | No | No | No | No |

---

## 3. The Democratic Trade Engine & Multi-Account Lifecycle

### 3.1 Dynamic Governance Parameters
The Strategist can fully configure and mutate the room's consensus execution logic parameters via the Room Settings configuration database:
* majority_threshold (Float): Decimal percentage of current online room Validators required to pass an open order (Default: 0.60).
* voting_window_seconds (Integer): Lifespan of an active execution proposal before automatic expiration (Default: 600).
* quorum_count (Integer): Minimum mandatory raw count of active votes required to cast for a consensus to be labeled valid (Default: 3).
* auto_execute (Boolean): Direct execution toggle flag. If true, the trade fires the exact microsecond the threshold condition is reached. If false, the order executes only after the entire window timer reaches zero.

### 3.2 Proposal System States & Runtime Loop
Every single /propose event triggers a structured lifecycle path inside the transactional state ledger:

[PROPOSAL_PENDING] ---> (Consensus Cleared) ---> [PROPOSAL_PASSED] ---> [ROUTING_TO_BROKERS] ---> [EXECUTED_SETTLED]
        |
        |---> (Timer Hits 0 w/o Threshold) ---> [CANCELLED_EXPIRED]
        |
        |---> (Asset Price Moves > 0.5%) ------> [CANCELLED_DRIFT]

### 3.3 Advanced Execution Edge Cases & Circuit Breakers

#### A. The Price Drift Guardrail
When an active proposal state shifts to PROPOSAL_PASSED, the backend queries the real-time Alpaca Market Data pipeline to capture the asset's current quote. If the live ask price has drifted >0.5% away from the proposal's declared limit parameters during the voting window, the entire sequence aborts. The state changes to CANCELLED_DRIFT and a warning card streams to the channel.

#### B. Partial Fill & Broker Fault Isolation
If a 60% upvote passes in a room of 10 people, orders are dispatched concurrently via individual async worker tasks. If a user has an invalid API token or insufficient buying power, their sub-routine emits a failure response. The orchestrator isolates the broken task, transitions it to EXECUTION_FAILED, and sends a private system alert to their dashboard—allowing the valid accounts to execute without stalling the group.

#### C. The Catch-Up Entry Window
If an offline room member misses a successful execution window, the system provides an opportunity to catch up. If the asset remains within a +/-0.5% price variance corridor of the group's initial entry signature, the interface displays a persistent [Catch-Up Entry] widget next to their user avatar string, giving them a one-click window to mirror the position late.

#### D. The Emergency Panic Protocol (/alphasignals-panic)
If a market position breaks down unexpectedly, any authorized Strategist or Signal Provider can execute a high-priority system command: /alphasignals-panic [TICKER]. 
* This command overrides all active chat streaming states, instantly rendering a prominent red alert layout block across the UI for all online members.
* It initializes a localized fast flash vote (requiring a reduced consensus threshold, e.g., 40%) or fires an automatic stop-out trigger depending on pre-set room risk parameters.
* Upon validation, the engine sends immediate, parallel market liquidation orders to all connected brokerage APIs to harvest capital back to cash states instantly.

---

## 4. UI/UX Design System & Real-Time Interaction Models

### 4.1 Spatial Layout & State-Driven Component Geometry
The application workspace utilizes a rigid, hardware-accelerated 3-panel flex grid layout. It leverages `react-resizable-panels` to allow user-customizable widths while strictly enforcing structural min/max boundaries to protect visibility.

* Global Workspace Wrapper:
  - Class: `h-screen w-screen overflow-hidden flex bg-neutral-950 text-neutral-50 antialiased`

* Panel 1: Navigation Sidebar (Left)
  - Layout Constraints: Default width `w-64` (256px). Minimum bounds: `w-48`. Maximum bounds: `w-80`.
  - Behavior: Collapsible via state variable `isLeftOpen: boolean`. When `false`, executes translation shift `translate-x--full` with hardware acceleration (`transform-gpu duration-200 ease-in-out`).
  - Key Binding Toggle: `Cmd + B` (Mac) / `Ctrl + B` (Windows).

* Panel 2: Interactive Main Canvas (Center)
  - Layout Constraints: Flex property set to `flex-1 h-full min-w-[450px] flex flex-col`.
  - Internal Composition: Divided into a scrolling chat viewport (`flex-1 overflow-y-auto p-6 scrollbar-thin scrollbar-thumb-neutral-800`) and a fixed input dock (`w-full border-t border-neutral-800 p-4 bg-neutral-950/80 backdrop-blur-md`).

* Panel 3: Grounded Context Shelf (Right)
  - Layout Constraints: Default width `w-80` (320px). Minimum bounds: `w-72`. Maximum bounds: `w-96`.
  - Behavior: Collapsible via state variable `isRightOpen: boolean`. Smoothly slides out of sight using layout clipping bounds (`transition-all duration-200 ease-in-out`).
  - Key Binding Toggle: `Cmd + J` (Mac) / `Ctrl + J` (Windows).

---

### 4.2 Token-Driven Theming & Aesthetic Semantics
To replicate a premium, institutional financial terminal vibe, all components rely strictly on Tailwind utility classes mapped to an un-aliased, highly dark semantic palette.

* Background Surfaces:
  - Base Layout Screen: `bg-neutral-950`
  - Floating Content Cards / Sidebars: `bg-neutral-900`
  - Structural Borders & Hairlines: `border-neutral-800`
  - Input Fields / Hover States: `bg-neutral-800/50` / `hover:bg-neutral-800`

* Financial Color Language:
  - Bullish / Action Clear / Growth States: `text-emerald-400` / `bg-emerald-950/40` / `border-emerald-800`
  - Bearish / Abort Protocols / Downward Risk: `text-rose-400` / `bg-rose-950/40` / `border-rose-800`
  - Intelligence / AI System Processes / Scribe Notes: `text-cyan-400` / `bg-cyan-950/40` / `border-cyan-800`

* Typography System:
  - General UI Labels & Navigation Elements: `font-sans antialiased tracking-tight text-neutral-200`
  - Numbers, Asset Prices, Stock Tickers, and Chat Prompts: `font-mono tracking-normal text-neutral-100`

---

### 4.3 Generative UI Hydration & Multi-State Component Lifecycle
We completely reject static text layouts for analytical workflows. Using the Vercel AI SDK and `@ai-sdk/elements`, the central chat stream intercepts backend token payloads to hydrate living React elements directly into the message feed.

#### Lifecycle State A: The Streaming Inference Phase (System Output)
* Trigger: The Classifier Judge routes a user request down an AGENTIC or TRADING path.
* UI Architecture: The stream injects an execution wrapper placeholder into the feed. It renders a clean skeleton loader framework utilizing standard pulsing loops (`animate-pulse bg-neutral-800 h-32 w-full rounded-xl border border-neutral-800`).
* AI Tracking Panel: A specialized sub-panel streams the model’s raw internal step-by-step reasoning tokens inside a clean, low-opacity text box (`text-xs text-neutral-400 font-mono italic`).

#### Lifecycle State B: The Active Consensus Phase (User Engagement)
* Trigger: The underlying parser converts the prompt data into an active proposal object, changing its state token to `PROPOSAL_PENDING`.
* UI Architecture: The skeleton container swaps out instantly, rendering an interactive voting card element. 
* Interactive Elements:
  - Progress Gauge: A real-time visual progress bar tracking current upvotes against room requirements (`w-full bg-neutral-800 h-2 rounded-full overflow-hidden`). The active fill color dynamically shifts from `bg-neutral-700` to `bg-emerald-500` the exact microsecond the 60% configuration target is crossed.
  - Action Control Row: Highly distinct UI buttons containing interactive tap indicators (`active:scale-98 transition-transform`).

#### Lifecycle State C: The Symmetric Settlement Phase (Locked Execution)
* Trigger: The proposal database updates to `PROPOSAL_PASSED`, `CANCELLED_EXPIRED`, or `CANCELLED_DRIFT`.
* UI Architecture: The voting card undergoes an automatic layout mutation. The user action buttons (`Upvote` / `Downvote`) fade out via smooth transitions (`opacity-0 pointer-events-none`).
* Data State Seal: The card layout seals itself behind an un-clickable asset wrapper card stamped with an immutable status icon (e.g., a solid green lock icon checkmark for executed trades, or a strike-through indicator for price drift cancellations).

---

### 4.4 Cross-Panel State Hydration Matrix
To maintain absolute cross-panel state synchronization during rapid chat interactions, the layout utilizes a unified client-side event bus (Socket.io websockets mirrored into a local Zustand memory store).

| Real-Time System Event | Originating Node (Trigger) | Target Node (Recipient) | Visual UI Lifecycle Mutation |
| :--- | :--- | :--- | :--- |
| `proposal_created` | Center Panel Chat Input (User enters text command) | Center Panel Viewport | Appends active Interactive Voting Card into message feed list. Resets text field state. |
| `vote_casted_websocket` | Center Panel Button Press (User clicks upvote button) | Center Panel Card | Instantly steps up the visual progress percentage gauge and increments raw tally count. |
| `proposal_passed` | Backend Consensus Engine (60% Threshold crossed) | Right Panel (Squad Trades) | Appends a new live tracking position row into the panel matrix using an entrance animation (`animate-in fade-in slide-in-from-right-4`). |
| `proposal_passed` | Backend Consensus Engine (60% Threshold crossed) | Center Panel Card | Changes button array states to locked layout. Replaces countdown timer element with active timestamp. |
| `position_liquidated` | Right Panel Panic Trigger (Strategist hits panic button) | Right Panel Drawer | Permanently flags target position row color scheme as `bg-rose-950/20 text-rose-400`, then fades it out entirely after a 5-second delay. |
| `room_gate_lockout` | Backend Billing Cron (User drops subscription status) | All Panels Layout Shell | Mounts full absolute blurred modal veil (`backdrop-blur-xl bg-neutral-950/90`) completely blocking inputs. |
---

## 5. Operational State Logic & The "Group Gate" Paywall

### 5.1 System Compliance Checker (Billing Hook)
To maintain our bottom-up user acquisition flywheel, chat text features are entirely free, but collaborative AI tools are group-gated.
* The Routine Scan: An internal system worker loops once every 60 minutes across all active room asset records.
* The Rule: Every member holding a role tier of Strategist, Signal Provider, or Validator must possess an active, verified subscription record matching billing tier STARTER or higher ($10/mo+).
* The Intercept Trigger: If even a single core member drops to a FREE account status, the entire room's structural state alters from COMPLIANT down to ROOM_LOCKED.

### 5.2 The Lockdown User Journey
* When a channel status hits ROOM_LOCKED, the center canvas hides the real-time chat input stream and displays a clean, locked modal overlay interface.
* The modal outputs an accountability compliance panel showing who is currently blocking activation: "Room Premium Locked. 7/8 Core Members Active. Features restore when @User upgrades to the Starter Tier."
* The Admin can click an inline action row to instantly kick the non-compliant user—dropping them to an Observer state or removing them entirely. This instantly restores full active system capabilities for the remaining members without breaking the room's momentum.

---

## 6. Implementation Roadmap

1. Sprint 1: The Workspace Interface Shell -> Build Next.js 15 template directories, Shadcn panel resizing, and state boundaries.
2. Sprint 2: The Classifier Judge Setup -> Construct LiteLLM API proxy endpoints and design the structured JSON routing layer.
3. Sprint 3: The Governance Engine & WebSockets -> Build out the server-side state machine tracking room upvote/downvote arrays and streaming updates via WebSockets.
4. Sprint 4: Multi-Account Broker Dispatch -> Securely map the concurrent Alpaca API order loops and install the Price Drift Guardrails.
5. Sprint 5: Stripe Integration & The Group Gate -> Connect the Stripe Connect Express webhooks to enforce automated room lockouts.

---