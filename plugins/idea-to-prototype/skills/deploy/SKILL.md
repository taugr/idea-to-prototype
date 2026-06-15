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

2. **Deploy** (confirm — this publishes their app). From the project folder:
   `npx vercel --yes`
   The first run links/creates the project (the defaults are fine) and returns a deployment URL. Vercel auto-detects Next.js and builds it.

3. **If the app has a server route that needs a secret** (the `ANTHROPIC_API_KEY` from `add-ai`): the key is **not** in the code — set it on Vercel, then redeploy so it takes effect.
   - `npx vercel env add ANTHROPIC_API_KEY production` → paste the key when prompted (the facilitator's shared, capped key).
   - **Redeploy** so the new var is picked up: `npx vercel --prod --yes`. *(Env-var changes only apply to deployments created **after** they're added — the redeploy is required, and "I set the key but it still fails" is almost always a missing redeploy.)*
   - If the app has no backend route, skip this step.

4. **Make the URL public — turn off Deployment Protection.** By default a deployment can be gated behind a **Vercel login wall**: the live URL shows "Authentication Required" to everyone, including demo-day visitors. For a public prototype, turn it off (confirm — it's a settings change):
   **vercel.com → the project → Settings → Deployment Protection → Vercel Authentication → Disabled → Save.**
   Then the URL is open to anyone. *(If they truly want it private, leave it on — but then only logged-in collaborators can see it, which is rarely what a demo wants.)*

5. **Give them the live URL** and have them open it in a **fresh/incognito browser** to confirm it loads with no login. If they set a key, run the real feature end-to-end on the live site.

## Hard rules

- **Confirm before every public/account action:** deploying, `vercel login`, `vercel env add`, and changing Deployment Protection. Show the exact command/step first.
- **Never commit secrets or API keys.** The key lives only in Vercel's env vars and in local `.env.local` (gitignored) — never in the code or a committed file. If you spot a key in the source or a commit, stop and remove it.
- **Set the key on Vercel, not in the code** — `vercel env add` + redeploy. Don't hardcode it or bake it into the build.
- Keep changes minimal — deploy/config only; do not refactor the app.
- If `vercel login` is needed, let them do the browser step; don't improvise auth.
- **"Authentication Required" on the live URL?** That's Deployment Protection still on — fix it in Settings (step 4), not in the code.
- **AI feature failing on the live site?** Check, in order: env var set for the **production** environment, **redeployed after** adding it, and the key valid/under its limit. The route returns a clean error message — read it.
