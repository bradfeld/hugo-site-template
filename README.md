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
| `package.json` | Pins Hugo in-repo via `hugo-extended@0.163.0` (Renovate watches this) |
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

## Sites with serverless `/api` functions

This template is npm + pure-static. If your site needs Vercel serverless functions
(like zeroknowledge.ink's proof easter egg), keep pnpm instead of npm and add
`pnpm.onlyBuiltDependencies: ["hugo-extended"]` to `package.json` — switching the
package manager mid-flight breaks Vercel's function dependency resolution. See the
Gotchas section of the [hugo-common README](https://github.com/bradfeld/hugo-common#gotchas-learned-the-hard-way).
