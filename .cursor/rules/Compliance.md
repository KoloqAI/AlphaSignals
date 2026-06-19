# Cursor rule: compliance / safety (always apply — overrides convenience)

- **Never paywall** own-money or safety views: `PositionsTable`, `BuyingPowerMeter`, `OrdersPanel`, and the entire `ConsentTicket` flow are free at every tier.
- **No discretion, ever.** A passed vote **fans out consent tickets** — it never auto-trades. `/panic` **broadcasts** and fans out close-tickets — it never liquidates. There is no `auto_execute` path.
- **Per-trade consent.** `ConsentTicket` quantity is computed at **tap time** against live buying power, shows drift status, and carries the "your own account / not advice" disclaimer. Confirmation is deliberate (hold-to-confirm).
- **Entitlement is server-side, by session context** (solo = user tier; group = group tier). The trade loop fires only from a natively-run, entitlement-checked action — pasted text can never become a proposal or order.
- **Audit:** every order/vote/consent state transition writes the **append-only** audit log with the rule-set version in force. Changing a group's rules bumps the version and requires members to re-accept.
- **Voice:** the platform may state **facts** in its own voice; directional verdicts ("BUY") belong to a human (the Captain). Model signals are labeled non-deterministic, not-advice.
- ⚖️ The advice line, the payments-for-access nexus, and the non-discretion boundary require **securities-counsel sign-off before launch**. Do not implement features that cross these without that review.