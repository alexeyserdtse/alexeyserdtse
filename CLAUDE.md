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
  --exclude 'github-readme-stats.vercel.app' \
  --exclude 'streak-stats.demolab.com' \
  --exclude 'ghchart.rshah.org' \
  --exclude 'readme-typing-svg.demolab.com' \
  --exclude 'capsule-render.vercel.app' \
  --exclude 'komarev.com' \
  '**/*.md'
```

CI (`.github/workflows/ci.yml`) runs markdownlint + lychee on every push/PR to `master`. The lint config disables `MD013` (line length), `MD033` (inline HTML — required, the README leans heavily on `<div align="center">`, `<img>`, and `<picture>`), and `MD041` (first-line heading — the README opens with an HTML banner).

## Architecture & moving parts

- **`README.md`** — single deliverable. Hand-authored HTML+Markdown, centered hero, then About / Experience / Tech Stack / Featured Project / GitHub Stats sections.
- **`assets/hero.svg`** — hand-authored SVG banner (not generated). Edit the SVG directly; it carries the name, a `// DATA ENGINEER`-style label, tagline, and a small "pipeline" node motif.

## The one rule that matters: cohesive palette

Every visual element shares one navy/blue theme. When adding or editing any widget, match these colors — do not introduce a new theme (the history shows a tokyonight experiment was deliberately replaced by this custom palette):

- Stat/graph cards share the hero family: background `#1B3E63`, title/accent `#8FD0FF`, icons `#9FC7EC`, text `#C7D8E8`, highlights `#F2F7FC`, `border_radius`/`radius` `18` (echoes the hero's rounded corners)
- Hero gradient `#2E6498 → #1B3E63`, hero accents `#8FD0FF` / `#9FD0FF`
- Top two cards use an equal fixed `height="170"` (NOT `width`) so they align flush side by side; below them the full-year contribution calendar (`ghchart.rshah.org`, has month labels) is `width="100%"`. The stats card hides zero/irrelevant metrics (`hide=stars,issues,contribs`) and shows commits/PRs/PRs-merged
- Do NOT add `include_all_commits=true` to the stats card — it triggers the rate-limited commit-search API and reliably fails with "Could not fetch total commits". Use the reliable Vercel streak host `streak-stats.demolab.com`, never the Heroku one

A change that adds a stats/streak/graph card or badge with mismatched theme params is wrong even if it renders. Keep stats, streak, activity graph, and hero visually consistent.

## Workflow notes

- Work happens via PRs to `master` (see squashed history `(#1)`…`(#11)`); CI must pass.
- This repo's content has been iterated against the owner's explicit preferences — prefer editing existing sections/SVG over redesigns, and confirm before swapping the visual style wholesale.
