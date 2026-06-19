# Cursor rule: UI / design (always apply)

- `docs/PRODUCT_SPEC.md` §4 is the **authoritative** UI/UX spec. `design/wireframes/*.html` are the **visual target**. `docs/UI_MANIFEST.md` maps routes → wireframes → components.
- Mirror the **token names** in `design/wireframes/_tokens.css` into the Tailwind theme (names canonical, not hex). Dark "terminal" theme.
- Build every component in §4.6 to its **documented states** (e.g. `ConsentTicket` has 10 states; `GatedSection` has 4). Don't ship a component without its empty/loading/error states.
- Use **mono font for all numbers/tickers/prices**; status must be **color + icon + text** (never color alone); keep **facts (deterministic)** and **model views (LLM)** in visually distinct zones; **never blur/tease the user's own data** — only premium analysis.
- **Mobile-first** (not desktop-with-breakpoints): the whole shell adapts — left rail → drawer, right panel → bottom sheet, nav → bottom tab bar, Markets → stacked column. `ConsentTicket`/`VoteCard` are touch bottom sheets (hold-to-confirm, targets ≥44px). Ship **PWA + web push** for calls / `/panic` / passed votes.
- Stack: Next.js 15 App Router, shadcn/ui, Tailwind, TradingView Lightweight Charts, Tabler icons, Vercel AI SDK. No browser localStorage in artifacts/components unless required.
