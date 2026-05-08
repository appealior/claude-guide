# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project shape

This repository is a **single-file Korean HTML book** — `index.html` — that introduces Claude Code workflows. There is no build step, no test suite, no package manifest, and no framework. Edits go directly into the HTML.

Adjacent files:
- `DESIGN.md` — design system reference ("Midnight Command Center" dark theme). Treat it as the source of truth for any styling decision.
- `docs/improvement-plan.md` — log of past Codex review ↔ Claude rebuttal decisions. Read it before changing the appendix or SVG diagrams; it explains *why* the current shape exists.

## Preview workflow

```
start index.html      # Windows
open index.html       # macOS
```

Open in the browser, edit, refresh. There is no dev server.

## Hard constraint: must work offline

The page **must render correctly with no internet access.** Anything that introduces a runtime network dependency is a regression.

Concretely:
- Fonts are embedded as base64 woff2 inside `<style>` (variable Inter / Space Grotesk + IBM Plex Mono 400, Latin subset only — Korean falls back to system fonts and that is intentional). Do not reintroduce `<link href="https://fonts.googleapis.com/...">`.
- All diagrams are inline `<svg>`. Do not add `<img>`, external CSS, or external JS.
- Hyperlinks in the body (`docs.claude.com`, etc.) are reference-only — they don't load anything for rendering. Leave them.
- If a new font weight or family is genuinely needed, embed it the same way (download the Latin-subset woff2 from gstatic, base64-encode, append a `@font-face` block with a `data:font/woff2;base64,...` `src`).

## Design rules (follow DESIGN.md)

When writing any CSS or SVG color/typography, consult `DESIGN.md` first. Specifically:

- **Palette**: only the tokens defined in DESIGN.md (`Midnight Abyss #05060f`, `Comet #d8ecf8`, `Celestial Light #b6d9fc`, `Neon Violet #663af3`, etc.). Never reintroduce the legacy beige/navy palette (`#1a1816`, `#6a665f`, `#2a3d66`, `#c4923a`, `#faf7f2`, etc.) — those were fully removed.
- **Typography**: Space Grotesk for display headings (no letter-spacing), Inter for body (with `-0.01em` tracking), IBM Plex Mono for code. Reserve `Neon Violet` strictly for the central CTA / focal element.
- **Surfaces**: cards use 12-16px radius with the `--glass-card` / `--glass-form` inset-shadow stacks already defined in `:root`. Pills (999px) for badges and small chips. Don't use flat opaque card backgrounds — keep the translucent + inset-shadow look.
- **Existing CSS variables**: reuse `--ghost-white`, `--celestial-light`, `--neon-violet`, `--text-faint`, `--good`, `--font-display`, `--font-body`, `--font-mono`, `--bg-elev`, `--border-subtle` — don't introduce parallel one-offs.

## SVG diagrams — gotchas

Each `<figure><svg>` block declares its own scoped `<style>` with literal color hex (not CSS variables — older renderers handle literals more reliably inside SVG `<defs>`). When editing or adding a diagram:

- Use the dark-theme literals already in use elsewhere (`#ffffff`, `#b6d9fc`, `#663af3`, `rgba(212,228,250,0.5)`, `#8cd6a3`, etc.). Grep an existing SVG for a working palette.
- For the **focal/central element** of a diagram (e.g. the LLM core in figure 3.3), use `#663af3` fill + `#b6d9fc` stroke + a `drop-shadow(0 0 14px rgba(102,58,243,0.45))` filter. White or arctic-mist text on neon-violet has the contrast we want; light-blue fill with light text **does not** — that was a fixed bug.
- Korean labels render via system fonts; the SVG `font-family` is for Latin glyphs.

## Content rules

- The guide is **Korean-language**, generic-developer-targeted (no domain bias). Past inline ERP/AcmeERP examples were intentionally generalized to TypeScript / Drizzle / PostgreSQL / `User`-`Order` examples. Do not reintroduce industry-specific terminology unless asked.
- Stack-specific commands in the appendix (A.1) must use placeholder syntax (`{프로젝트의 마이그레이션 검증 명령}`) plus 2-3 stack examples — never a single hard-coded command, since the appendix promises generic reuse.
- Print mode (`@media print`) is intentionally maintained — preserve it when changing card or SVG styles.

## Deployment

- The repo is connected to Vercel via Git Integration. Pushes to `main` auto-deploy to production; PR pushes create preview deploys.
- The HTML file is `index.html` (renamed from `claude-guide.html` so Vercel serves it at `/` with no rewrite needed).
- `vercel.json` only configures `Cache-Control: must-revalidate` on the HTML so updates ship immediately. No build step, no framework preset.

## Git conventions

- Commit messages: brief, focused, no `Co-Authored-By` trailer (per global CLAUDE.md preference).
- The project is a small personal artifact; no PR / review pipeline beyond ad-hoc Codex reviews triggered by the user.
