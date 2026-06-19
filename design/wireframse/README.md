# Alpha-Signals — Wireframes & Design Reference (Phase 1)

Low-fidelity HTML/SVG wireframes for the Phase-1 UI plus the design tokens. They are the
**visual reference** for `PRODUCT_SPEC.md` §4 (the written spec is the source of truth).

## View
Open any `.html` in a browser. All files share `_tokens.css` (dark "terminal" tokens) and load
Tabler icons from CDN. Keep `_tokens.css` in the same folder as the HTML files.

## Files (reading order)
- `00-ia-two-gravities` — IA: group vs personal "two gravities"
- `01-shell-four-states` — the context-aware shell in 4 states
- `02-solo-chat-landing` — solo fresh-chat landing
- `03-solo-chat-active` — solo active chat (research card + pinned/summary)
- `04-solo-markets-takeover` — solo Markets terminal (search/chart/tools/positions)
- `05-closed-group-chat` — closed group: inline vote + active-votes stack
- `06-group-call-to-consent-ticket` — group call -> personal consent ticket (the membrane)
- `07-trade-option-analysis` — trade/option analysis surface (focused chain)
- `08-gated-section-states` — GatedSection: tier-locked / quota-exhausted / group-locked
- `09-leakage-native-vs-pasted` — native card vs pasted blob (contextual entitlement)
- `10-formation-flow` — closed-group formation (two paths)
- `11-member-join-gate` — member join gate (upgrade / fund / accept rules)
- `12-captain-configure-group` — captain create/configure group

## Recommended project structure (so Cursor builds the right thing)
```
repo/
  docs/PRODUCT_SPEC.md           # source of truth (esp. §4 UI/UX, §5–6 architecture)
  design/wireframes/*.html       # these files (visual target)
  design/wireframes/_tokens.css  # tokens — mirror into your Tailwind/theme config
  .cursor/rules/ui.md            # rule: treat PRODUCT_SPEC.md §4 as authoritative,
                                 # these wireframes as the visual target; map each
                                 # component in §4.6 to a real component.
```

## Notes
- Token **names** (not exact hex) are canonical — wire them into your theme.
- Wireframes are LOW fidelity: structure, states, layout intent — not final visuals.
- Filenames map to the §4.6 Component inventory in the spec.
