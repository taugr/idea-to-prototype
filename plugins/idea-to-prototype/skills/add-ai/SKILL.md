---
name: add-ai
description: Make ONE faked feature real by adding a small server API route (on Vercel) that calls Claude with a key kept server-side as an environment variable — never in the browser or the repo. Drops static export, adds the route with error handling, wires the front-end, keeps a mock fallback. Optional "go live" step; deploys via the deploy skill.
---

# Add AI (server-side key)

You wire **one** faked feature to a **real Claude model** by adding a tiny **server API route**. The key lives **server-side** as an environment variable — never in the browser, never in the repo. The participant is a non-developer; **you do the typing, they direct, run, and verify.**

A server route needs a server, so the app deploys to **Vercel** (via `/idea-to-prototype:deploy`), not a static host. The facilitator provides one **shared, spend-capped** API key that gets set as the env var and **revoked after** the workshop — students never handle a key.

## When to use this

After the prototype runs (`/idea-to-prototype:build`). Pick the **one feature that is the wedge** — the single step the idea hinges on — and make just that real. Everything else stays mocked.

## Flow

1. **Pick the one real feature.** Read how it's currently faked (often `data.jsx`) — the real call replaces that one path; the mock stays as the fallback.

2. **Drop static export.** If `next.config` has `output: 'export'` (and any leftover `basePath` / `images.unoptimized` / `trailingSlash`), **remove them** — a server route can't run under static export. The build will *silently* succeed but drop the route, so the AI call fails only after deploy. A minimal config is correct:
   ```js
   const nextConfig = {}
   export default nextConfig
   ```

3. **Install the SDK** (in the app folder): `npm install @anthropic-ai/sdk`.

4. **Add the server route** at `app/api/run/route.js` — it runs on the server, reads the key from the environment, and **handles errors cleanly** (no key → clear message; bad key / rate limit / network → clean message, never a raw crash):
   ```js
   import Anthropic from "@anthropic-ai/sdk"

   export async function POST(req) {
     const apiKey = process.env.ANTHROPIC_API_KEY
     if (!apiKey) {
       return Response.json({ error: "Server not configured (missing ANTHROPIC_API_KEY)." }, { status: 500 })
     }
     try {
       const { input } = await req.json()
       const client = new Anthropic({ apiKey })
       const res = await client.messages.create({
         model: "claude-opus-4-8",
         max_tokens: 2048,
         messages: [
           { role: "user", content: `…your prompt for the wedge, including ${input}…` },
         ],
       })
       const text = res.content.find((b) => b.type === "text")?.text ?? ""
       return Response.json({ text })
     } catch (err) {
       return Response.json({ error: "AI request failed — check the key and limits, then try again." }, { status: 502 })
     }
   }
   ```
   - **Server-side, so NO `dangerouslyAllowBrowser`.** Prompt the wedge precisely; if you need structured data back, ask for JSON and `JSON.parse` it.
   - **Model:** default `claude-opus-4-8`. For one shared, capped class key, the facilitator may switch to a cheaper model (e.g. `claude-haiku-4-5`) to stretch the budget — their call.
   - The no-key guard + `try/catch` are required, not optional — without them a missing or over-limit key returns an ugly unhandled 500.

5. **Call it from the front-end** — a `"use client"` helper that `fetch`es the route, with a **mock fallback** so the app still works if the key isn't set or the call fails:
   ```js
   "use client"
   export async function runRealFeature(userText) {
     const r = await fetch("/api/run", {
       method: "POST",
       headers: { "content-type": "application/json" },
       body: JSON.stringify({ input: userText }),
     })
     if (!r.ok) throw new Error("api-error")
     return (await r.json()).text
   }
   ```
   Wrap the caller in try/catch → on error, show the mocked sample. The app never crashes without a key.

6. **Local dev key — `.env.local`, never committed.** Put the key at the project root so the route works locally:
   ```
   ANTHROPIC_API_KEY=sk-ant-…
   ```
   `.env.local` is gitignored by create-next-app (`.env*`) — **confirm it is**, and never commit the key. Then `npm run dev`, trigger the feature, confirm a **real** response; remove the key and confirm the **mock fallback** shows.

7. **Deploy with the key server-side.** Run `/idea-to-prototype:deploy` — it sets the same key as a **Vercel env var** (`vercel env add ANTHROPIC_API_KEY production`) and redeploys. The deployed site calls your route; the key is never sent to the browser.

## Facilitator note

Use **one dedicated, spend-capped** Anthropic key for the class, set it as the env var (local `.env.local` for testing + Vercel for the live site), and **revoke it after**. After revocation the deployed apps fall back to the mock (the route returns a clean error). The spend cap is the real protection — students never see the key.

## Hard rules

- **The key is a server-side env var — NEVER in the browser, the code, or a commit.** No `dangerouslyAllowBrowser`. `.env.local` (gitignored) locally, a Vercel env var when deployed.
- **Drop `output: 'export'`** — a server route needs a server; the static build silently drops the route otherwise.
- **The route must handle errors** (no-key guard + `try/catch`) so a missing / bad / over-limit key returns a clean message, not a crash.
- **One feature.** Make the wedge real; leave the rest mocked. Don't rebuild the app.
- **Always keep the mock fallback** so the app works with no key (and after the key is revoked).
- Before committing, grep the diff for the key — it must appear nowhere in tracked files.
