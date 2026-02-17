# agents.md

## Design contract (authoritative)
Before doing ANY UI work (layout, CSS, components, copy hierarchy), you MUST read `design.md`.
Treat `design.md` as the single source of truth for visual style, tokens, component rules, and bans.

Rules:
- Never ask the user style questions if `design.md` exists.
- Never invent new style rules that conflict with `design.md`.
- If a task seems to require a style change, update `design.md` only when the user explicitly requests it via `CHANGE_DNA:` or `RESET_DNA`.
- If `design.md` is missing, initialize it once (see “One-time DNA initialization”).

## State-of-the-art structure (default, no questions)
Default layout unless the repo already has a strong pattern:

- `design/` — Design DNA + tokens (authoritative for UI)
- `docs/architecture/` — module boundaries + ADRs (authoritative for structure decisions)
- `src/app/` — app shell, routing, providers
- `src/features/<feature>/` — feature modules (domain + ui + tests)
- `src/ui/` — shared UI primitives only (Button/Input/etc.)
- `src/lib/` — small, stable utilities (no feature logic)
- `src/services/` — API clients/adapters
- `src/state/` — global state only if unavoidable

Module rules:
- No long files: **split at ~250 LOC** (hard) and **~150 LOC** (soft).
- No circular deps between features.
- Features may depend on `ui/`, `lib/`, `services/`; never the reverse.
- Prefer composition over inheritance; prefer pure functions for `lib/`.
