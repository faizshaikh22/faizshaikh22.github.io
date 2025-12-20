# Faiz Blog

Personal blog built with **Hugo (extended)** + **PaperMod**, styled around the **Mr. Robot / Red Wheelbarrow BBQ** aesthetic (dark “terminal” vibe + red accents), with a clean light mode variant.

Live: `https://faizshaikh.xyz/`

## Repo layout (high level)

- **Content**: `content/`
- **Site config**: `hugo.toml`
- **Theme** (git submodule): `themes/PaperMod/`
- **Custom styling**: `assets/css/extended/custom.css`
- **Custom head includes (fonts)**: `layouts/partials/extend_head.html`
- **GA4 injection**: `layouts/partials/google_analytics.html`
- **Schema JSON override (fixes Hugo minify build)**: `layouts/partials/templates/schema_json.html`
- **Custom domain for GH Pages**: `static/CNAME` (copied into `public/CNAME` on build)

## Prereqs

- **Git**
- **Hugo extended v0.152.2** (match CI)
  - Verify:

```powershell
hugo version
```

Expected: `hugo v0.152.2 ... +extended`

## Run locally (PowerShell)

### First-time setup (submodules)

```powershell
cd D:\Projcets\blog
git submodule update --init --recursive
```

### Dev server

Fast dev:

```powershell
hugo server -D
```

“Realistic” dev (recommended before pushing, avoids fast-render weirdness):

```powershell
hugo server --disableFastRender -D
```

Open the printed URL (Hugo will pick a different port if 1313 is taken).

## Build locally (production-equivalent)

This is the same class of build your GitHub Actions deploy runs.

```powershell
cd D:\Projcets\blog
hugo --minify
```

Output goes to `public/`.

## Pre-deploy checklist (do this before pushing)

- **Build passes**:

```powershell
hugo --minify
```

- **Theme submodule is present** (CI needs it):

```powershell
git submodule status
```

- **Quick UX smoke test** (local server):
  - Home, Posts, a Post, Tags, Archives, Search
  - Theme toggle (light/dark)

## Deployment (GitHub Pages)

This repo deploys via GitHub Actions:

- Workflow: `.github/workflows/hugo.yml`
- Trigger: push to `main`
- Hugo version pinned to **0.152.2 extended**
- Uses `actions/configure-pages` and builds with:
  - `hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"`
- Uploads `./public` to Pages

### Custom domain (`faizshaikh.xyz`)

Custom domain is committed as:

- `static/CNAME` → becomes `public/CNAME` on build

In GitHub:

- Repo → **Settings → Pages**
  - Source: **GitHub Actions**
  - Custom domain: `faizshaikh.xyz`
  - Enable **Enforce HTTPS**

## Analytics (GA4)

GA4 Measurement ID is configured in `hugo.toml`:

- `services.googleAnalytics.ID = "G-83CQP2BNGW"`

The script is injected by:

- `layouts/partials/google_analytics.html`

Note: `params.analytics.google.SiteVerificationTag` is **site verification meta**, not analytics; it’s kept separate.

## Writing posts

Posts live in `content/posts/*.md`.

Example front matter:

```toml
+++
title = "My Post"
date = "2025-12-20T00:00:00Z"
draft = false
+++
```

## Notes / gotchas

- **Don’t edit the theme directly** unless you intend to maintain a fork. Prefer overrides in `layouts/` and CSS in `assets/`.
- If Hugo says “port 1313 already in use”, it’ll auto-pick a new port. Use the URL it prints.


