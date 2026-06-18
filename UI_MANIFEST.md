# Alpha-Signals.ai: Complete UI & Component Manifest

**Version:** 1.0  
**Status:** Design System Locked  
**Target Architecture:** Next.js 15 (App Router), Tailwind CSS, Shadcn/ui, Radix Primitives

---

## 1. Global Authentication & Onboarding Views

### 1.1 /login — The Gateway View

- **Purpose:** Secure, high-conversion entry portal for new and returning users.
- **UI Primitives (src/components/ui/):** Button, Card, Input, Label.
- **Feature Components (src/features/auth/components/):**
  - `login-card.tsx`: A centered, minimalist container with subtle background gradients, handling state transitions between login, signup, and password recovery.
  - `oauth-button.tsx`: Reusable button primitives for single-tap Discord and Google authentication loops. Imports custom brand SVGs natively and handles loading/disabled states during handshake.

### 1.2 /account — Profile & API Custody Dashboard

- **Purpose:** The private configuration ground where users adjust billing details and authorize external broker tokens.
- **UI Primitives (src/components/ui/):** Tabs, Separator, Switch, Dialog.
- **Feature Components (src/features/account/components/):**
  - `profile-summary-card.tsx`: Displays the authenticated user’s avatar image string, display name, account creation timestamp, and a custom tier badge primitive (Free, Starter, Pro, Elite).
  - `stripe-billing-portal-widget.tsx`: Houses account tier statuses, a renewal countdown meter, and a primary action trigger linking straight to secure Stripe Connect Checkout sessions.
  - `broker-connection-card.tsx`: Renders independent toggle slots for Alpaca and Robinhood. Contains input vectors for public/secret credentials and tracks real-time API handshake validity statuses via status dots (bg-emerald-500 for active, bg-rose-500 for disconnected).
  - `allocation-cap-input.tsx`: A numeric text entry lock down input field that updates the user's maximum transactional risk boundary variable (Virtual Allocation Cap).

---

## 2. Main Dashboard Shell (/dashboard)

The core application workspace utilizes a hardware-accelerated, flexible layout container.

- **Layout Component (src/app/dashboard/layout.tsx):** Instantiates the `<ResizablePanelGroup>` primitive to partition space into three panels with custom keyboard tracking overrides (`Cmd+B` to toggle Left Panel, `Cmd+J` to toggle Right Panel).
- **Global Workspace Layout State:**
  - Left Panel: Navigation Sidebar (Width: `w-64`, Collapsible)
  - Center Panel: Main Interactive Canvas (Flex: `flex-1`, Fluid Layout)
  - Right Panel: Grounded Context Shelf (Width: `w-80`, Collapsible)

---

## 3. Left Panel Components: The Navigation Sidebar

### Location: src/features/navigation/components/

- `sidebar-container.tsx`: The root layout shell for the left pane. Uses a deep dark background style (`bg-neutral-900 border-r border-neutral-800 h-full flex flex-col`).
- `user-session-footer.tsx`: Anchored at the absolute bottom of the side panel. Renders the user's micro-avatar image, active user name text string, and a clickable settings icon routing back to the `/account` zone.
- `navigation-link-item.tsx`: Atomic text rows handling basic navigation redirects (e.g., Standalone AI Sandbox, Global Execution Logs). Employs subtle hover styles (`hover:bg-neutral-800 text-neutral-300 active:text-neutral-50`).
- `syndicate-section-header.tsx`: A small label text block (`text-xs font-mono text-neutral-500 tracking-wider uppercase`) with a floating plus icon trigger matching the action `Create New Syndicate Room`.
- `syndicate-room-row.tsx`: Interactive navigation rows displaying custom room name fields.
  - *Contents:* A small, low-opacity indicator icon showing active role access levels (`S` for Strategist, `P` for Provider, `V` for Validator). Includes a persistent badge tracking the real-time vote count if a trade proposal is currently active in that room.

---

## 4. Center Panel Components: The Interactive Main Canvas

### Location: src/features/research/components/ & src/features/syndicate/components/

- `canvas-container.tsx`: The central workspace stage. Manages the dynamic layout transition from a raw text stream view to advanced, structured workspace environments.
- `message-stream-viewport.tsx`: A highly performant, scrollable channel containing historical chat arrays. Handles layout rendering for standard system text strings, error alerts, and code fragments.
- `generative-ui-wrapper.tsx`: Intercepts JSON payload actions passed by the Vercel AI SDK to replace flat text outputs with rich React UI components.
  - *Sub-component:* `pulse-skeleton-loader.tsx` -> A smooth, pulsing content placeholder container used during live backend agent calculations (`animate-pulse bg-neutral-800 h-32 w-full rounded-xl`).
- `interactive-voting-card.tsx`: The central interaction asset of the application.
  - *Contents:* A title header text string identifying the transaction structure (e.g., `[NVDA Equity Buy]`). Text metrics showing entry price limits, expiration timers, and allocation sizing values. A dual visual bar tracker displaying current team consensus progress ratios. Two distinct control buttons: `👍 Upvote` and `👎 Downvote`.
- `prompt-input-dock.tsx`: The baseline terminal panel fixed to the bottom canvas boundary.
  - *Contents:* Integrates Vercel AI Elements primitives for rich streaming and screenshot support. Houses quick-action suggestion pills (e.g., `"/propose "`, `"/research "`). Includes a visual icon trigger processing user text submissions.

---

## 5. Right Panel Components: The Grounded Context Shelf

### Location: src/features/syndicate/components/

- `shelf-container.tsx`: Root vertical container managing room tracking summaries. Set to a clean layout layer (`bg-neutral-900 border-l border-neutral-800 h-full flex flex-col`).
- `room-scribe-summary.tsx`: An encapsulated card module parsing background data context. It renders rolling bulleted overviews of the conversation topics handled by the Center Panel chat thread to capture thesis points at a glance.
- `aggregate-portfolio-widget.tsx`: Renders room performance health visuals.
  - *Contents:* Aggregate cash values, total open asset valuation metrics, and room P&L performance strings (`text-emerald-400` for gains, `text-rose-400` for losses).
- `active-position-row.tsx`: Atomic layout blocks showing positions currently held by the room.
  - *Contents:* Bold ticker text layout elements, raw size volumes, average group cost boundaries, and individual micro-kill action buttons for high-velocity liquidations.
- `pinned-artifact-item.tsx`: Document links pinning historical research data records. Clicking an entry hydates a visual layout block (like an interactive chart or full financial sheet analysis) directly back into the Center Panel canvas stage.

---

## 6. Absolute Overlay & Modal Components

### Location: src/features/billing/components/ & src/components/shared/

- `compliance-lockout-veil.tsx`: A full absolute screen intercept block triggered when a room status passes to `ROOM_LOCKED`. Enforces a heavy blur layout (`backdrop-blur-xl bg-neutral-950/90 flex flex-col items-center justify-center`). Renders an alert graphic detailing account discrepancies and flags the non-compliant user profile elements blocking active room operations.
- `panic-protocol-overlay.tsx`: High-priority, full-width screen banner injected when a user runs the `/alphasignals-panic` override command. Employs a bright, alert color style (`bg-rose-950 text-rose-400 border border-rose-800 animate-pulse p-4 rounded-lg flex items-center justify-between`). Renders immediate, global portfolio liquidation control triggers.

---

## 7. Cursor Implementation Prompt Blueprint

When initializing your code editing session, pass this exact prompt block to your Cursor composer input field to utilize this manifest:

"Analyze the layout, design system variables, and structural properties detailed in UI_MANIFEST.md alongside the directory map listed inside STRUCTURE.md and the product requirements in PRODUCT_SPEC.md. Generate the complete suite of atomic UI components for the dashboard shell using Next.js 15 App Router syntax, standard Tailwind utility classes, and Shadcn component tokens. Keep components isolated, single-responsibility, strictly typed with TypeScript, and free of placeholder code."