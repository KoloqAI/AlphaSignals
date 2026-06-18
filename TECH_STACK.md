# Alpha-Signals.ai: Technical Stack & Infrastructure Addendum

## 1. Middleware & Routing Layer
This layer handles the "Classifier Judge" logic, request validation, and proxying.

* **LiteLLM Proxy:** The primary gateway for all AI interactions. It handles load balancing between AWS Bedrock (Claude 3.5 Sonnet) and local/hosted open-source models (Qwen/Llama).
* **Next.js Middleware:** Used for Edge-level authentication checks. Every request to `/dashboard/*` is verified for a valid session token before reaching the main runtime.
* **WebSocket Gateway (Socket.io):** Handles bi-directional communication. Required for the Democratic Voting engine to push live "vote count" updates to all connected syndicate members without page refreshes.
* **Rate Limiter (Upstash Redis):** Protects the `/propose` endpoint. Ensures that even if an Admin misconfigures a rule, the system cannot be flooded with excessive API calls to brokerage providers.

## 2. Backend Services & Execution Engine
* **API Runtime:** Node.js 20+ (TypeScript) running on a serverless/containerized environment (Vercel Functions or AWS Lambda).
* **State Machine:** A custom engine using **XState** to manage the lifecycle of a trade (Pending -> Consensus -> Executing -> Settled). This prevents "race conditions" where two people vote at the exact same millisecond.
* **Database (PostgreSQL via Drizzle ORM):**
    * *Why Drizzle?* It provides excellent TypeScript type safety and is lighter than Prisma, keeping our cold-start times fast.
* **Task Queue (BullMQ + Redis):** Manages the "Pre-Flight Checks." When a trade is proposed, the proposal is pushed to a queue; BullMQ ensures that broker API verification happens sequentially and reliably.

---

## 3. The Open-Source Tech Stack Inventory

We are using a "best-of-breed" open-source strategy to minimize vendor lock-in and keep costs near zero.

| Category | Tool / Library | Strategic Value |
| :--- | :--- | :--- |
| **Framework** | **Next.js 15** | Industry standard, React Server Components (RSC) for speed. |
| **UI Components** | **Shadcn/ui** | Radix-based, fully accessible, zero-dependency visual blocks. |
| **Styling** | **Tailwind CSS** | Atomic, utility-first styling for consistent design tokens. |
| **State Management**| **Zustand** | Extremely lightweight, boilerplate-free state management. |
| **Backend ORM** | **Drizzle ORM** | Type-safe, high-performance database communication. |
| **AI Orchestration**| **LiteLLM** | The single point of failure for all LLM calls; easy to swap models. |
| **AI UI/Streaming** | **Vercel AI SDK** | Best-in-class for streaming LLM outputs into React components. |
| **Validation** | **Zod** | Essential for ensuring JSON outputs from the "Classifier Judge" match our schema. |
| **Real-time** | **Socket.io** | Stable, battle-tested standard for real-time WebSocket communication. |
| **Auth** | **NextAuth.js (Auth.js)**| The gold standard for OIDC/OAuth2 integrations in Next.js. |

---

## 4. Architectural Rules for Cursor
When asking Cursor to write code, apply these constraints:

1.  **No Monoliths:** Every feature inside `/features` must be atomic (one file = one purpose).
2.  **Type Safety:** All API responses (especially from LLMs) MUST be validated using **Zod** schemas before they touch the database.
3.  **Security First:** Broker API keys must NEVER be stored in plain text. They must be encrypted using `AES-256-GCM` before hitting the database.
4.  **No Hardcoding:** Governance constants (Voting Thresholds, Windows) must be fetched from the database, not hardcoded as constants in the frontend.