---
name: deploy
description: Deploy the participant's Next.js app to a live Vercel URL — sign in the Vercel CLI, deploy, set any server-side env vars (like the AI key) and redeploy, turn off Deployment Protection so the URL is public, and return the live link. Workshop Session 4. Beginner-safe; confirms before every public or account action.
---

# Deploy (Vercel)

You publish the participant's app to **Vercel** so it has a live URL — and, if they added a server feature (the API route from `/idea-to-prototype:add-ai`), so that backend runs for real with the key kept server-side. The participant is a non-developer; explain plainly and **confirm before anything public or account-touching** (deploying, signing in, setting env vars, changing project settings).

Assumes the project is the **Next.js app from the `build` skill** (it runs locally). Deploy works whether or not it has a backend route. No GitHub repo is required — the Vercel CLI uploads the project directly.

## Preconditions

- **Node + npm** (the CLI runs via `npx vercel`). If missing, route to `/idea-to-prototype:setup-check`.
- **A Vercel account, signed in.** Check with `npx vercel whoami`. If it errors or is blank, tell them you'll sign them in and run **`npx vercel login`** (a browser/email flow) **after they say go** — account sign-in is theirs to approve. They can sign up with their GitHub login in one click.

## Flow

1. **Confirm it runs locally** first (`npm run dev`). Don't deploy something that doesn't build.

2. **Deploy to production** (confirm — this publishes their app). From the project folder:
   `npx vercel --prod --yes`
   The first run links/creates the project (defaults are fine); Vercel auto-detects Next.js and builds it. **Always pass `--prod`** so the **live** URL updates — a plain `npx vercel` only makes a throwaway preview deployment with its own temporary URL, which confuses people ("my site didn't change"). Every later run redeploys the **same** project and updates the **same** live URL (see "Publishing later changes").

3. **If the app has a server route that needs a secret** (the `ANTHROPIC_API_KEY` from `add-ai`): the key is **not** in the code — set it on Vercel, then redeploy so it takes effect.
   - `npx vercel env add ANTHROPIC_API_KEY production` → paste the key when prompted (the facilitator's shared, capped key).
   - **Redeploy** so the new var is picked up: `npx vercel --prod --yes`. *(Env-var changes only apply to deployments created **after** they're added — the redeploy is required, and "I set the key but it still fails" is almost always a missing redeploy.)*
   - **(Optional) sync the key to local dev** — so `npm run dev` uses the real model too, pull it down instead of re-typing it: `npx vercel env pull .env.local --environment=production`. Vercel is the **one place** the key is entered; local follows. `.env.local` is gitignored — never commit it.
   - If the app has no backend route, skip this step.

4. **Make the URL public — turn off Deployment Protection.** By default a deployment can be gated behind a **Vercel login wall**: the live URL shows "Authentication Required" to everyone, including demo-day visitors. For a public prototype, turn it off (confirm — it's a settings change):
   **vercel.com → the project → Settings → Deployment Protection → Vercel Authentication → Disabled → Save.**
   Then the URL is open to anyone. *(If they truly want it private, leave it on — but then only logged-in collaborators can see it, which is rarely what a demo wants.)*

5. **Give them the live URL** and have them open it in a **fresh/incognito browser** to confirm it loads with no login. If they set a key, run the real feature end-to-end on the live site.

## Publishing later changes

There's no auto-deploy — whenever they change the code and want it live again, **re-run the deploy**:
- `npx vercel --prod --yes` (or just re-run `/idea-to-prototype:deploy`).
- It detects the existing project via the hidden `.vercel/` link and **updates the same live URL** — no new project, no new URL. (Vercel created `.vercel/` on the first deploy; it stays on their machine.)
- **Env vars persist** across deploys — they don't re-enter the key. (Only if they *change* a var does this redeploy apply it.)
- Remind them to **`git commit`** too: committing is their local restore point; `npx vercel --prod` is what the public sees — two separate steps.

## Hard rules

- **Confirm before every public/account action:** deploying, `vercel login`, `vercel env add`, and changing Deployment Protection. Show the exact command/step first.
- **Never commit secrets or API keys.** The key lives only in Vercel's env vars and in local `.env.local` (gitignored) — never in the code or a committed file. If you spot a key in the source or a commit, stop and remove it.
- **Set the key on Vercel, not in the code** — `vercel env add` + redeploy. Don't hardcode it or bake it into the build.
- **Always deploy with `--prod`** (first run and every re-run) so the live URL updates; a plain `vercel` makes a preview that doesn't change the shared URL.
- Keep changes minimal — deploy/config only; do not refactor the app.
- If `vercel login` is needed, let them do the browser step; don't improvise auth.
- **"Authentication Required" on the live URL?** That's Deployment Protection still on — fix it in Settings (step 4), not in the code.
- **AI feature failing on the live site?** Check, in order: env var set for the **production** environment, **redeployed after** adding it, and the key valid/under its limit. The route returns a clean error message — read it.
