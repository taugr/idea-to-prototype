---
name: build
description: Turn a Claude Design handoff into a running Next.js app, configured as a static site that can later deploy to GitHub Pages. Scaffolds the project, ports the design's screens faithfully, runs the dev server, and gets the first screen rendering. Workshop Session 3 (Build Sprint 1).
---

# Build

You turn the participant's Claude Design handoff into a *running* web app. The participant is a non-developer; **you do the typing, they direct, run, and verify.** Explain each step in plain language.

The goal for this session is narrow: **get it running — the project builds and the handoff's first screen renders in a browser.** Navigation is a stretch. Real logic, a backend, and deploy are later sessions. Do not over-build.

## Before you start

Confirm **Node ≥ 20** (`node -v`), npm, and git are present. If anything's missing or Node is older, stop and tell them to run **`/idea-to-prototype:setup-check`** first.

## Flow

1. **Get + unzip the handoff.** Ask for the downloaded zip (or the design URL). **If it's a zip, unzip it first** (`unzip handoff.zip -d handoff/`). The export is **raw React/JSX** (e.g. `app.jsx`, `*-screen.jsx`, `styles.css`, `icons.jsx`, `data.jsx`) plus a standalone `*.html` shell — **NOT a Next.js project; you will convert it.** Read the `.jsx` files to learn the screens, components, and styles. **Ignore the standalone `.html`** — it's a preview wrapper, not a page you port. This design is the **source of truth**; you will not redesign it.

2. **Scaffold Next.js non-interactively** (a bare `create-next-app` hangs on prompts a non-dev can't answer). Match the handoff: plain CSS/JSX is the common case —
   ```
   npx create-next-app@latest my-prototype --js --app --no-tailwind --no-eslint --no-src-dir --no-turbopack --import-alias "@/*"
   ```
   Use `--tailwind` instead of `--no-tailwind` only if the handoff's CSS is Tailwind; use `--ts` only if the handoff is TypeScript. **Do all work inside the new `my-prototype/` folder.** Then write `next.config.mjs`:
   ```js
   const nextConfig = {
     output: 'export',
     images: { unoptimized: true },
     trailingSlash: true,
     // if you scaffolded with --ts, also add (a stray type error shouldn't block a prototype):
     // typescript: { ignoreBuildErrors: true }, eslint: { ignoreDuringBuilds: true },
   }
   export default nextConfig
   ```
   Add an empty `public/.nojekyll` (so GitHub Pages won't choke on `_next/` later).

3. **Port the design faithfully.** The export is a single-bundle React app whose root (`app.jsx`) usually switches screens with `useState`. Simplest faithful port: copy the component files into `app/` (or `app/components/`), render the root from `app/page.jsx`, and **import the global CSS once in `app/layout.jsx`** (not page.jsx) so tokens/fonts apply everywhere. **Any file that uses `useState`, `onClick`, or other interactivity MUST start with the line `"use client"`** — without it the build fails with *"you're importing a component that needs useState… Client Component."* Add it to every ported screen/component. Keep the export's own `useState` screen-switching for now. **Preserve the look exactly** — do not invent a new aesthetic or "tidy up."

4. **Mock everything dynamic.** No backend, no API routes, no server actions, no real data fetching. The export's `data.jsx` is already mock data — use it. The app must run fully static. *(One feature can be made real later via `/idea-to-prototype:add-ai` — a browser-side, bring-your-own-key Claude call that stays static. For now, mock it.)*

5. **Run it.** Tell them you'll install dependencies, then `npm install`, then `npm run dev`. Tell them to open **the URL Next prints** (usually http://localhost:3000; it may pick another port). Iterate until **the handoff's first screen renders** — not the Next.js starter page. That's the milestone — celebrate it.

6. **Navigation (only if time).** Wire the core screens together. **Prefer plain static routes** (`app/about/page.jsx`); avoid `[param]` dynamic routes — static export needs `generateStaticParams` for those and it trips people up.

7. **Fix breakages with them.** The most common first error is a **Server/Client Component mismatch** ("needs useState…") → add `"use client"` to the top of the file named in the error. Next most common is a CSS/import path that doesn't resolve → fix the path, don't restyle. Read the error, explain it briefly, fix minimally, re-run.

8. **Checkpoint.** Make sure you're **inside the new app folder** (not a parent repo). `git init` if it isn't already its own repo, set the branch with `git branch -M main`, then `git add -A && git commit -m "First working version"` (this includes `package-lock.json`, which the deploy step needs). Tell them this is their safe restore point.

9. **Close.** Confirm it runs. Note that Session 4 makes the workflow real and sets up the GitHub Pages deploy.

## Hard rules

- **Static-export-safe ONLY:** no API routes, no server actions, no `getServerSideProps`, no runtime secrets, no database, no `[param]` dynamic routes. Everything client-side or mocked.
- **Preserve the handoff design** — never substitute your own styling. If a screen is missing, build the minimum that matches the existing tokens.
- **Stay in scope:** running + first screen. No auth, no tests, no extra features, no premature deploy.
- Work inside the new app folder; commit the lockfile.
- If the build fights you for more than a couple of tries on one error, pick the simplest path that renders something and move on — momentum over polish.
