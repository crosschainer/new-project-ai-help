# design.md — UI Rules (stable, off-default, no questions)

## Prime directive
Do NOT produce generic "Tailwind/shadcn SaaS" UI. Maintain a single distinctive Design DNA across the repo.

---

## Hard BANLIST (non-negotiable)
You are not allowed to produce a generic Tailwind/shadcn SaaS look.

Banned patterns:
- No "soft shadow + rounded-xl/2xl + gray background card grid" aesthetic.
- No default blue CTA (no typical blue-500/600 primary).
- No hero-left text / hero-right screenshot marketing layout by default.
- No “icon in a circle” feature list blocks.
- No pill buttons everywhere.

Enforcement:
- If any banned pattern appears in new code, remove it immediately.
- If existing UI contains these patterns, refactor away from them opportunistically when touching related files.

---

## One-time Design DNA initialization (DO THIS ONCE, NO QUESTIONS)

### Files created (authoritative, repo-local)
If missing, create:
- `design/ui-seed.txt`  (one-time unique seed)
- `design/design-dna.md` (human-readable design contract)
- `design/tokens.css`    (CSS variables / tokens)

After creation, treat them as the source of truth. Never regenerate silently.

### Seed (works for empty repos)
**Authoritative seed file**
- If `design/ui-seed.txt` exists: use its contents verbatim as `SEED_STRING`.
- If it does NOT exist:
  - Create `design/` folder.
  - Generate a high-entropy seed and write it to `design/ui-seed.txt`:
    - Format: `ui-seed:v1:<uuid-v4>` OR `ui-seed:v1:<48 hex chars>`
  - Do NOT base the seed on time, machine path, username, or hardware identifiers.
  - Set `SEED_STRING` to the file contents.

**Optional enrichment (only if files already exist)**
After creating the seed file, you MAY append any of these (if present) to `SEED_STRING` for extra determinism:
- repo directory name
- README title line
- package/project name (package.json / pyproject.toml / Cargo.toml)
Do not block on these.

Record the final seed used at the top of `design/design-dna.md`.

---

## DNA Generator (signature-based, not a template)
If `design/design-dna.md` is missing, generate a distinctive DNA from `SEED_STRING` with the rules below.
No archetype names. No A/B/C options. No user questions.

### 1) Spacing scale (MUST NOT be default 4/8/16 ladder)
Choose BASE from: 5, 6, 7, 9, 10, 11, 12 (derived from seed).
Choose a rhythm mode from:
- additive: [B, 2B, 3B, 4B, 6B, 8B]
- mixed:    [B, 2B, 3B, 5B, 8B]
- dense:    [B, 2B, 3B, 4B, 5B, 6B]
This is the only allowed spacing set.

### 2) Radius rule (avoid rounded-xl/2xl)
Choose one radius family (derived from seed):
- sharp:    0–3px only
- tight:    2/4/6px
- contrast: 2px for controls, 10–12px for panels (never > 12px by default)
No ad-hoc radii.

### 3) Borders vs shadows (ban soft SaaS elevation)
Default: border-first. Shadows OFF.
If shadows exist, they must be hard/ink-like and rare. Never soft/glowy and never the primary structure.

### 4) Typography scale (explicit, consistent)
Choose ratio from: 1.125, 1.2, 1.25, 1.333 (from seed).
Choose base font size 14–16px (from seed).
Generate 6 sizes using the ratio (rounded to whole px).
Heading line-height: tight. Body line-height: normal.
Use tabular numerals for numeric UI if possible.

### 5) Accent color (MUST NOT be default blue)
Pick an accent hue H from the seed with constraints:
- H must NOT be in 190–235 degrees (blue band).
Define neutrals (warm/cool) from seed.
Accent usage restriction: focus + links + primary action only.
No gradients by default.

### 6) Layout grammar (anti-card-grid)
Select exactly TWO structural rules from:
- gridlines/dividers are primary structure
- split panes (sidebar/main) with strong section headers
- striped lists / table-first layout
- panel stacks with deliberate rhythm (not card soup)
Write them as MUST rules.

### 7) Component invariants (make consistency automatic)
Set and store:
- button height
- input height
- focus style (visible + deterministic, e.g., 2px outline with offset)
- link style (must look like a link)
- default section/panel padding (from spacing scale)

---

## Non-generic gate (must pass)
After generating the DNA, verify it is not generic. It must satisfy at least 4/6:
- Spacing BASE is not 4 or 8.
- Radius does not exceed 12px by default.
- No soft shadows as a general elevation system.
- Accent is non-blue and restricted in usage.
- Structure is not primarily "cards in a grid".
- Links are visually distinct.

If it fails:
- Regenerate once using `SEED_STRING + "|v2"` and record that in `design/design-dna.md`.
Do not loop further.

---

## Tokens (design/tokens.css is authoritative)
Write tokens as CSS variables at minimum:
- spacing tokens: `--space-1...`
- radius tokens: `--r-...`
- type tokens: `--font-size-...`, `--line-...`, `--font-weight-...`
- colors: `--bg`, `--fg`, `--muted`, `--border`, `--accent`, `--accent-contrast`, `--focus`

Implementation rules:
- Prefer tokens over raw values.
- No one-off hex colors (except inside tokens file).
- No ad-hoc spacing/radius outside the defined scales.

---

## Stability: NO DRIFT, NO QUESTIONS
- If `design/design-dna.md` exists, follow it exactly.
- Do not suggest style changes, do not "modernize", do not drift.
- Do not ask the user to choose styles.
- Do not introduce new scales unless necessary; if necessary, minimally extend tokens + DNA once.

---

## Explicit commands (only these may change DNA)
- `RESET_DNA`:
  - recreate `design/ui-seed.txt`
  - regenerate `design/design-dna.md` and `design/tokens.css`
- `CHANGE_DNA: <instruction>`:
  - update DNA + tokens to satisfy the instruction, keeping banlist constraints

---

## Skeptical mode (anti-flattery)
Do not say "looks great". Do not optimize for engagement.

---
