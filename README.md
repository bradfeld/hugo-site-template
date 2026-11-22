# hugo-site-template

Scaffold for a new site on the Feld Hugo-on-Vercel content network
(feld.com, foundry.vc, adventuresinclaude.ai, zeroknowledge.ink, …).

Start a new site from this template and it inherits the whole network convention
on day one — the Hugo version can't drift, dependencies auto-update, and a broken
render can't ship silently. The convention itself lives in
[bradfeld/hugo-common](https://github.com/bradfeld/hugo-common); this repo is the
ready-to-clone starting point.

## What's in the box

| File | Role |
|---|---|
| `package.json` | Pins Hugo in-repo via `hugo-extended@0.169.0` (Renovate watches this; the hugo-common preset holds it below 0.170.0, which ships no macOS extended build) |
| `vercel.json` | Builds with `node_modules/.bin/hugo` and an env-aware `-b` (correct preview URLs) |
| `renovate.json` | Extends the shared `hugo-common` preset → smoke-gated automerge of Hugo bumps |
| `.github/workflows/smoke.yml` | Calls the reusable `hugo-common` smoke gate (asserts the site RENDERS, not just builds) |
| `themes/hugo-common` | Git submodule providing shared infra partials (`preview-noindex`, …) |
| `hugo.toml` | Minimal config; `theme = ["hugo-common"]`, `params.productionHost` for preview noindex |
| `layouts/` | Barebones buildable home — **replace with your design** |
| `.gitignore` | The network's standard ignore set |

## Create a new site from it

1. **Use this template** on GitHub (or `gh repo create <name> --template bradfeld/hugo-site-template`).
2. Clone with submodules: `git clone --recurse-submodules <url>` (or `git submodule update --init` after a plain clone).
3. `npm install` — provisions the pinned Hugo to `node_modules/.bin/hugo`.
4. Set `baseURL`, `title`, and `params.productionHost` in `hugo.toml` to your real domain.
5. Add your design (drop in a theme as `theme = ["your-theme", "hugo-common"]`, or build out `layouts/`).
6. Create the Vercel project, point it at the repo. The `vercel.json` build command needs no Hugo env var.
7. Confirm the **Renovate** GitHub App covers the new repo (it does automatically if installed on *All repositories*).

That's it — pinned, auto-updating, and smoke-verified from the first commit.

## Browser editor

`hugo-common` ships a `sveltia-cms` page layout that loads [Sveltia CMS](https://github.com/sveltia/sveltia-cms) (a pinned, integrity-checked version), so a site owner can edit content in a browser and commit to GitHub. This template mounts an example at `/editor-example/`.

To mount one on your site:

1. Add one content file whose front matter selects the layout and keeps the page unlisted (copy `content/editor-example.md`):
   ```yaml
   ---
   title: "Editor"
   layout: sveltia-cms
   url: /<your-path>/
   build:
     list: never
   sitemap:
     disable: true
   outputs: [html]
   ---
   ```
2. Add `static/<your-path>/config.yml` (copy `static/editor-example/config.yml`, set `backend.repo` to your repository, and define your collections).

Each real site must choose its own unguessable `<your-path>` and never ship `editor-example` to production. The address only keeps the page out of casual view, so it is not the protection: only people with GitHub write access to the repository can save anything. The shared `smoke` check fails the build if an editor page loses its integrity hash or `noindex` tag, or shows up in the sitemap or RSS feed.

## Sites with serverless `/api` functions

This template is npm + pure-static. If your site needs Vercel serverless functions
(like zeroknowledge.ink's proof easter egg), keep pnpm instead of npm and add
`pnpm.onlyBuiltDependencies: ["hugo-extended"]` to `package.json` — switching the
package manager mid-flight breaks Vercel's function dependency resolution. See the
Gotchas section of the [hugo-common README](https://github.com/bradfeld/hugo-common#gotchas-learned-the-hard-way).

## Merging

Pull requests into `main` merge only after the `smoke` check passes.
