# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static bilingual one-page contact site for Code By AB, built with Hugo,
deployed via GitHub Actions to GitHub Pages on every push to `main`. No
CMS — content is two hand-edited Markdown files
(`content/_index.sv.md` / `content/_index.en.md`), edited directly in an
editor/IDE.

## Machine setup

- **Hugo Extended** required (tested with v0.165.0). On Windows if `hugo`
  isn't on PATH: `winget install --id Hugo.Hugo.Extended`. winget does
  **not** update PATH for the current shell session — find the binary
  under `%LOCALAPPDATA%\Microsoft\WinGet\Packages\Hugo.Hugo.Extended_*\hugo.exe`
  and prepend its directory to PATH for that session, or restart the
  shell.
- **No Node.js needed** — there's no Pagefind/search and no other JS
  build step in this project.
- Nothing else to install — no `npm install`, no dependency lockfiles.

## Commands

- `hugo server -D` — local dev server with live reload at
  http://localhost:1313/, drafts visible.
- `hugo --minify` — production build to `public/`. `draft: true` content
  is excluded automatically — this is the *only* publish mechanism, never
  add `-D`/`--buildDrafts` to anything meant to represent production.
- GitHub Actions (`.github/workflows/hugo.yml`) runs `hugo --minify` on
  every push to `main` and deploys `public/` via
  `actions/deploy-pages@v4`. Repo Settings → Pages → Source must be set
  to "GitHub Actions" (one-time manual step, not stored in this repo).

No test suite, no linter. Hugo's own build is the correctness check — it
fails loudly on broken templates/frontmatter. After a change, run
`hugo --minify` and spot-check `public/`.

## Architecture

- **Bilingual via Hugo's "multiple_files" i18n**: `content/_index.sv.md`
  and `content/_index.en.md` sit side by side. `defaultContentLanguage =
  "sv"`, `defaultContentLanguageInSubdir = false`, so Swedish serves at
  `/` and English at `/en/`.
- **Deliberately single-page**: one homepage, no sections, no
  taxonomies, no menus, no page bundles. Don't add scaffolding
  (`list.html`, extra sections, an `i18n/` string table) for content that
  doesn't exist yet — discuss with the user first if the site is about
  to grow beyond one page.
- **No third-party theme**: `layouts/`, `assets/`, `static/` live at repo
  root, not under `themes/`.
- **draft/publish**: `draft: true/false` frontmatter is the entire
  publish mechanism, no custom logic on top.
- **CSS**: hand-written, no framework or npm build step.
  `assets/css/{tokens,main,components}.css` are concatenated, minified,
  and fingerprinted via Hugo Pipes in `layouts/partials/head.html`
  (`resources.Concat` + `minify` + `fingerprint`, gated on
  `hugo.IsProduction` so dev serves unminified/unfingerprinted CSS).
  Colors/spacing/font-stack live as CSS custom properties in
  `tokens.css`. No self-hosted fonts — the site uses a plain system font
  stack, there's nothing to load.
- **Internal links must be relative** (`relLangURL`, `.RelPermalink`),
  anywhere a user clicks (logo, language switch). `.Permalink`/`absURL`
  is reserved for SEO metadata (canonical, OG, hreflang) in
  `layouts/partials/seo.html`, where an absolute production URL
  (`https://codeby.se/...`, matching the apex `CNAME`) is actually
  correct.
- **`static/CNAME`**: must live under `static/`, not repo root — only
  `static/` content reaches `public/`, which is what
  `actions/upload-pages-artifact` deploys. GitHub Pages reads the
  `CNAME` file from the deployed artifact, not from the repo root.
