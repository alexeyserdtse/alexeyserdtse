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
  --exclude 'readme-typing-svg.demolab.com' \
  --exclude 'capsule-render.vercel.app' \
  --exclude 'api.iconify.design' \
  --exclude 'komarev.com' \
  '**/*.md'
```

CI (`.github/workflows/ci.yml`) runs markdownlint + lychee on every push/PR to `master`. The lint config disables `MD013` (line length), `MD033` (inline HTML — required, the README leans heavily on `<div align="center">`, `<img>`, and `<picture>`), and `MD041` (first-line heading — the README opens with an HTML banner).

## Architecture & moving parts

- **`README.md`** — single deliverable. Hand-authored HTML+Markdown, centered hero, then About / Experience / Tech Stack / Featured Project / GitHub Stats sections.
- **`assets/hero.svg`** — hand-authored SVG banner (not generated). Edit the SVG directly; it carries the name, a `// BIG DATA ENGINEER`-style label, tagline, and a small "pipeline" node motif. The motif is animated with SMIL (`animateMotion` dots flowing along the paths + a pulse on the sink node); all animated elements carry `class="flow"` and are hidden by an embedded `prefers-reduced-motion` style — keep any new animation behind that class, subtle and slow (~3s cycles).
- **`assets/project-card.svg`** — hand-authored Featured Project card styled like the hero (same gradient, radius, type). Used instead of the github-readme-stats "pin" API on purpose (that host is down; see below) — keep project cards hand-authored and local.
- **Light-mode variants** — every local SVG asset has a `-light.svg` twin (light gradient `#F8FBFE → #E4EEF8`, `#C7D8E8` hairline border, text in `#1B3E63`/`#2E6498`, accents `#2A8BF2`/`#5FA8E8`/`#8FD0FF`). The README serves them via `<picture>` with `media="(prefers-color-scheme: light)"` sources; the `<img>` fallback is always the dark variant. When editing any asset, apply the same change to both variants — they must never drift.

## The one rule that matters: cohesive palette

Every visual element shares one navy/blue theme. When adding or editing any widget, match these colors — do not introduce a new theme (the history shows a tokyonight experiment was deliberately replaced by this custom palette):

- Stat/graph cards share the hero family: background `#1B3E63`, title/accent `#8FD0FF`, icons `#9FC7EC`, text `#C7D8E8`, highlights `#F2F7FC`, `border_radius`/`radius` `18` (echoes the hero's rounded corners)
- Hero gradient `#2E6498 → #1B3E63`, hero accents `#8FD0FF` / `#9FD0FF`
- The GitHub Stats section shows a single centered streak card. There used to be a github-readme-stats card beside it, but the shared public instance (`github-readme-stats.vercel.app`) was paused by its owner (503 `DEPLOYMENT_PAUSED`, see anuraghazra/github-readme-stats#4737) and rendered as a broken image. Do not re-add a card on that host unless the owner self-hosts an instance; if restored, reuse the old palette params (`bg_color=1B3E63&title_color=8FD0FF&icon_color=9FC7EC&text_color=C7D8E8`, `border_radius=18`) and put both cards back in a 2-cell `<table>` with equal fixed `height="155"`. No contribution graph/calendar (removed per preference).
- Tech Stack renders as uniform 48×48 monochrome icons via Iconify's `simple-icons` set, all one palette color (`?color=%232A8BF2`). This is the only way to get equal-size square tiles covering every tool — the colored `cdn.simpleicons.org` lacks dbt/AWS, and full-color `logos` sets have mismatched aspect ratios. Keep them one color and same size; don't reintroduce text badges here.
- The streak card caches for a fixed 24h (`cache_seconds` is ignored), and GitHub's image proxy caches on top. To force a fresh render, bump the `cache_bust=<date>` value on the streak URL — changing it makes a new cache key (service + camo), so the card refreshes immediately. The card ships two URLs in a `<picture>`: dark (`background=1B3E63…`) and light (`background=F8FBFE&stroke=C7D8E8&ring=2E6498&fire=2A8BF2&currStreakNum=1B3E63&sideNums=1B3E63&currStreakLabel=2E6498&sideLabels=2E6498&dates=8FA8C0`) — bump `cache_bust` on both together.
- Do NOT add `include_all_commits=true` to the stats card — it triggers the rate-limited commit-search API and reliably fails with "Could not fetch total commits". Use the reliable Vercel streak host `streak-stats.demolab.com`, never the Heroku one

A change that adds a stats/streak/graph card or badge with mismatched theme params is wrong even if it renders. Keep stats, streak, activity graph, and hero visually consistent.

## Workflow notes

- Work happens via PRs to `master` (see squashed history `(#1)`…`(#11)`); CI must pass.
- This repo's content has been iterated against the owner's explicit preferences — prefer editing existing sections/SVG over redesigns, and confirm before swapping the visual style wholesale.
