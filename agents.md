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

## Testing (fast-by-default, web apps include Playwright)
Rules:
- Always add/update tests for behavior changes and bug fixes (no exceptions unless explicitly told).
- Prefer the cheapest test that catches the bug:
  1) unit tests (pure logic)
  2) integration tests (components/services with minimal mocking)
  3) E2E (Playwright) only for critical user journeys

Structure (default unless repo already differs):
- Unit/integration: colocate near code (`__tests__` or `*.test.*`)
- E2E: `e2e/` (Playwright), with `playwright.config.*` and a small set of stable smoke flows.

Playwright E2E requirements (full integration):
- Maintain a SMALL suite of “journey” tests that run the real app (build + serve/dev) and verify:
  - app loads + navigation works
  - primary CTA flow (create/edit/save/submit) works
  - auth/session flow if applicable
  - one “regression” test per previously fixed production bug (when relevant)
- Use stable locators: prefer `getByRole(...)` / accessible names; fall back to `data-testid` (avoid brittle CSS selectors).
- Tests must be deterministic: no arbitrary sleeps; use explicit waits for UI state; avoid flakiness.

Rate-limit / compute discipline (do not waste cycles):
- Do NOT run the entire test suite by default. Run the smallest relevant subset (changed package/feature/test file).
- If scripts exist, use them; otherwise default to:
  - unit/integration: targeted file or project test filter
  - E2E: only the affected Playwright spec(s) or smoke project
- Keep new E2E tests minimal (1–3 per feature max) and prefer extending existing journeys over adding many small tests.
- In responses, report results briefly (what was tested + pass/fail). No long logs unless failing.

## Visual verification (UI changes must be checked on mobile + desktop)
When a change affects UI (CSS, components, layout, tokens, templates), perform a visual check:

### What to capture (minimal, no waste)
- Capture screenshots for the affected page(s)/state(s) only.
- Always capture exactly TWO viewports:
  - Mobile: 390×844
  - Desktop: 1280×800
- If the app supports themes, capture Light only by default; capture Dark only when the change touches colors/tokens/theme logic.

### How to capture
- Prefer Playwright to navigate to the relevant route(s) and capture screenshots.
- Use stable navigation and deterministic waits (no arbitrary sleeps).

### How to evaluate (Design DNA alignment)
- Read `design/design-dna.md` (and `design/tokens.css` if present) and verify screenshots align with:
  - BANLIST constraints (no soft-shadow rounded card soup, no default blue CTA, etc.)
  - spacing/radius/type/color rules from the DNA
  - hierarchy: primary action is visually dominant, links look like links
  - common breakages: overflow, misalignment, inconsistent padding/radius, cramped mobile layout

### Rate-limit discipline
- Do NOT screenshot every route. Only routes impacted by the change.
- Do NOT produce verbose commentary. Fix the concrete issues.
- If blocked from running the app or Playwright, fall back to deterministic checks (tokens/banlist) and state what couldn’t be verified visually.

### Baselines (optional, only if already present)
- If visual baselines exist in `e2e/baselines/`, compare and only analyze diffs.
- Do not introduce a large baseline system unless explicitly requested.
