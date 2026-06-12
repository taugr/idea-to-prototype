---
name: deploy
description: Deploy the participant's Next.js static-export prototype to GitHub Pages — connect/create a repo, configure Pages-safe settings, add a deploy workflow, push, and return the live URL. Workshop Session 4. Beginner-safe; confirms before every remote or public action.
---

# Deploy

You publish the participant's prototype to **GitHub Pages** so it has a live URL. The participant is a non-developer; explain plainly and **always confirm before any action that touches their GitHub account or makes something public** (creating a repo, pushing, changing repo settings).

Assumes the project is the **Next.js static-export app from the `build` skill** (committed, with `package-lock.json`). If it isn't, tell them to run `/idea-to-prototype:build` first.

## Preconditions

Check: `git --version`, `git config user.name/email`, and **GitHub auth** with `gh auth status`. If `gh` isn't authenticated, explain you'll sign them in and run `gh auth login` (browser flow) **after they say go**. If `gh` isn't installed, route them to `/idea-to-prototype:setup-check`.

## Flow

1. **Confirm static-export-ready.** `next.config` has `output: 'export'` and `images.unoptimized: true`; `public/.nojekyll` exists.

2. **Repo — check what already exists first.** Run `git remote -v`.
   - **`origin` already set** → don't create anything; confirm the URL and skip to step 3.
   - **They have a GitHub repo but no remote** → `git remote add origin <url>` (confirm).
   - **No repo at all** → ask for a name, confirm it'll be **public** (free Pages needs public), and create it **without pushing yet**:
     `gh repo create <name> --public --source=. --remote=origin`
     *(Creating the repo is a remote/public action — confirm first.)*
   - **If they insist on a private repo:** tell them GitHub Pages won't publish from a private repo on the free plan — offer public, or stop. Never run `--private` expecting Pages to work.

3. **Pages-safe basePath (substitute the REAL repo name).** A project site serves at `https://<user>.github.io/<repo>/`, so the build needs a basePath — but only in the deployed build. In `next.config`, replace `<repo>` with the actual repo name from step 2:
   ```js
   const repo = 'YOUR-REPO-NAME'  // must exactly match the GitHub repo
   const isProd = process.env.NODE_ENV === 'production'
   const nextConfig = {
     output: 'export',
     images: { unoptimized: true },
     trailingSlash: true,
     basePath: isProd ? `/${repo}` : '',
   }
   ```
   Do **not** add `assetPrefix` — `basePath` already handles `_next/`. Note for them: if they ever preview the built `out/` locally it'll look broken (assets point at `/<repo>/`) — that's expected; it's correct on Pages. Files referenced from `public/` (like `/logo.png`) need the `/<repo>` prefix on Pages, so prefer inline/imported assets.

4. **Branch = main.** `git branch -M main` (so the workflow's `on: push: [main]` actually triggers — `git init` on older git defaults to `master`).

5. **Add the deploy workflow** at `.github/workflows/deploy.yml`:
   ```yaml
   name: Deploy to GitHub Pages
   on:
     push: { branches: [main] }
   permissions:
     contents: read
     pages: write
     id-token: write
   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
         - uses: actions/setup-node@v4
           with: { node-version: 20 }
         - run: npm install
         - run: npm run build
         - uses: actions/configure-pages@v5
           with: { enablement: true }
         - uses: actions/upload-pages-artifact@v3
           with: { path: ./out }
     deploy:
       needs: build
       runs-on: ubuntu-latest
       environment: { name: github-pages, url: "${{ steps.deployment.outputs.page_url }}" }
       steps:
         - id: deployment
           uses: actions/deploy-pages@v4
   ```

6. **No manual Pages toggle.** The workflow self-enables Pages via `configure-pages`'s `enablement: true` on its first run — so just commit and push. *(You can't pre-enable Pages on a fresh empty repo anyway; the API 404s until there's content, which is why we let the workflow turn it on after the push. Verified live. If your org blocks auto-enablement, enable it once in **Settings → Pages → Source: GitHub Actions** and re-run the workflow.)*

7. **Commit + push** (confirm — this is the first push): `git add -A && git commit -m "Add Pages deploy" && git push -u origin main`. The push triggers the workflow, which enables Pages and deploys.

8. **Give them the URL.** `https://<user>.github.io/<repo>/` (the user part is **lowercased**). First deploy takes ~1–2 min (longer on a brand-new account) — watch the **Actions** tab; refresh the URL once the run is green.

## Hard rules

- **Confirm before every remote/public action:** creating a repo, the first push, and changing Pages settings. Show the exact command first.
- **Never commit secrets or API keys.** This is a public static site. (If they later wire real AI via `/idea-to-prototype:add-ai`, the participant pastes their key in the browser at runtime — it still must never be committed or baked into the build.)
- Keep changes minimal — config + workflow + branch only; do not refactor the app.
- If `gh` auth or install is missing, route to `/idea-to-prototype:setup-check`; don't improvise auth.
- **Blank page after deploy?** Open browser devtools → Network. If assets request `/<file>` (404) instead of `/<repo>/<file>`, the basePath is wrong — check `const repo` exactly matches the repo name. Fix that, don't rebuild the app.
- If the workflow fails, read the Actions log and fix the smallest thing (usually basePath, the branch, or a build error); push again.
