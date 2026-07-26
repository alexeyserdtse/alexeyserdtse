# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is the special **GitHub Profile repo** (`alexeyserdtse/alexeyserdtse`). Because the repo name matches the owner's username, `README.md` renders at the top of the GitHub profile page. The README *is* the product — there is no application code. The owner is Alexey Serdtse, a Data Engineer; keep all content factually aligned with his real profile (role, stack, certs, location).

## Commands

No build step. Two checks gate the README, both run in CI and both runnable locally:

```bash
# Markdown lint (config: .markdownlint-cli2.jsonc)
npx markdownlint-cli2 "**/*.md"

# Link check (mirror the CI exclusions so dynamic badge services don't fail)
npx lychee --no-progress \
  --exclude 'linkedin.com' \
  --exclude 'readme-typing-svg.demolab.com' \
  --exclude 'capsule-render.vercel.app' \
  --exclude 'api.iconify.design' \
  '**/*.md'
```

CI (`.github/workflows/ci.yml`) runs markdownlint + lychee on every push/PR to `master`. The lint config disables `MD013` (line length), `MD033` (inline HTML — required, the README leans heavily on `<div align="center">`, `<img>`, and `<picture>`), and `MD041` (first-line heading — the README opens with an HTML banner).

## Architecture & moving parts

- **`README.md`** — single deliverable. Hand-authored HTML+Markdown, centered hero + highlights strip, then About / Tech Stack / Experience / Featured Project. Section order is recruiter-scan-optimized (identity and proof numbers first, keywords second, depth after) — don't reorder without asking.
- **`assets/hero.svg`** — hand-authored SVG banner (not generated). Edit the SVG directly; it carries the name, a `// BIG DATA ENGINEER`-style label, tagline, and a small "pipeline" node motif. The motif is animated with SMIL (`animateMotion` dots flowing along the paths + a pulse on the sink node); all animated elements carry `class="flow"` and are hidden by an embedded `prefers-reduced-motion` style — keep any new animation behind that class, subtle and slow (~3s cycles).
- **`assets/project-card.svg`** — hand-authored Featured Project card styled like the hero (same gradient, radius, type). Used instead of the github-readme-stats "pin" API on purpose (that host is down; see below) — keep project cards hand-authored and local.
- **Light-mode variants** — every local SVG asset has a `-light.svg` twin (light gradient `#F8FBFE → #E4EEF8`, `#C7D8E8` hairline border, text in `#1B3E63`/`#2E6498`, accents `#2A8BF2`/`#5FA8E8`/`#8FD0FF`). The README serves them via `<picture>` with `media="(prefers-color-scheme: light)"` sources; the `<img>` fallback is always the dark variant. When editing any asset, apply the same change to both variants — they must never drift.

## The one rule that matters: cohesive palette

Every visual element shares one navy/blue theme. When adding or editing any widget, match these colors — do not introduce a new theme (the history shows a tokyonight experiment was deliberately replaced by this custom palette):

- Cards and chips share the hero family: background `#1B3E63`, title/accent `#8FD0FF`, icons `#9FC7EC`, text `#C7D8E8`, highlights `#F2F7FC`, radius `16–18` (echoes the hero's rounded corners)
- Hero gradient `#2E6498 → #1B3E63`, hero accents `#8FD0FF` / `#9FD0FF`
- There is deliberately NO GitHub stats/streak/graph section. The streak card was dropped because the owner's real work lives in private employer repos, so public counts read as low activity — a bad recruiter signal. (Separately, the github-readme-stats public instance is dead: 503 `DEPLOYMENT_PAUSED`, anuraghazra/github-readme-stats#4737.) Do not re-add stats, streak, activity-graph, or calendar cards unless the owner asks.
- `assets/highlights.svg` (+ `-light` twin) is the recruiter-scan strip under the contact badges: three static chips (~7 yrs · 130 TB warehouse · 30 min → 6 dbt runtime). The numbers duplicate the Experience section on purpose (headline + detail) — when a fact changes, update both. Keep the strip static; the hero is the only animated element.
- Tech Stack renders as uniform 48×48 monochrome icons via Iconify's `simple-icons` set, all one palette color (`?color=%232A8BF2`). This is the only way to get equal-size square tiles covering every tool — the colored `cdn.simpleicons.org` lacks dbt/AWS, and full-color `logos` sets have mismatched aspect ratios. Keep them one color and same size; don't reintroduce text badges here.
A change that adds any card, chip, or badge with mismatched theme params is wrong even if it renders. Keep every visual element consistent with the hero.

## Workflow notes

- Work happens via PRs to `master` (see squashed history `(#1)`…`(#11)`); CI must pass.
- This repo's content has been iterated against the owner's explicit preferences — prefer editing existing sections/SVG over redesigns, and confirm before swapping the visual style wholesale.
