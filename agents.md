# agents.md

## Design contract (authoritative)
Before doing ANY UI work (layout, CSS, components, copy hierarchy), you MUST read `design.md`.
Treat `design.md` as the single source of truth for visual style, tokens, component rules, and bans.

Rules:
- Never ask the user style questions if `design.md` exists.
- Never invent new style rules that conflict with `design.md`.
- If a task seems to require a style change, update `design.md` only when the user explicitly requests it via `CHANGE_DNA:` or `RESET_DNA`.
- If `design.md` is missing, initialize it once (see “One-time DNA initialization”).
