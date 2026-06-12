---
name: add-ai
description: Make ONE faked feature of the prototype real by calling the Claude API directly from the browser with a key the participant pastes at runtime — stored in the browser, never committed. Keeps the static GitHub Pages architecture (no backend). Optional "go live" step after the prototype builds and deploys. The key never enters the repo.
---

# Add AI (bring-your-own-key)

You wire **one** faked feature of the participant's prototype to a **real Claude model**, so the magic actually happens instead of being mocked. The participant is a non-developer; **you do the typing, they direct, run, and verify.** Explain each step plainly.

This stays **fully static** — no backend, no server, no API routes. The prototype calls the Claude API **straight from the browser**, using a key the participant **pastes at runtime**. That key lives only in their browser (`localStorage`); it is **never written into the code or committed.**

## When to use this

After the prototype builds (`/idea-to-prototype:build`) and ideally after it deploys (`/idea-to-prototype:deploy`). This is the optional "make it real" beat — pick the **one feature that is the wedge** (the single step the whole idea hinges on, e.g. "turn this message into a checklist") and make just that real. Everything else stays mocked.

## The honest tradeoff (say this out loud)

A static site can't hide a secret — anything in the page is public. So we don't put a key in the site; **each user pastes their own.** The Anthropic SDK calls this browser mode `dangerouslyAllowBrowser`, and it's named "dangerously" for a real reason: **the key is exposed in the browser.** For a short workshop with a **dedicated, spend-capped key you revoke at the end**, that risk is bounded and fine. For a real public launch, you'd move the key server-side (Vercel + an API route) instead — out of scope here.

## Facilitator setup (do this before class)

- Make a **dedicated API key** at console.anthropic.com with a **low spend limit** (its own workspace budget cap), so 25 people sharing it can't run up a surprise bill.
- Plan to **revoke it when the workshop ends.** After revocation the sites still load; only the live AI calls stop (and they fail softly — see the fallback below).

## Flow

1. **Pick the one real feature.** Confirm which mocked action becomes real. Keep it to one. Read how it's currently faked (often the export's `data.jsx`) — the real call replaces that one path; the mock stays as the fallback.

2. **Install the SDK** (inside the app folder): `npm install @anthropic-ai/sdk`.

3. **Add a key field — paste at runtime, store in the browser, NEVER hardcode.** A small settings input (type `password`) that saves to `localStorage`. Make clear it is never committed and never sent anywhere except Anthropic.
   ```jsx
   "use client"
   // a tiny settings control
   <input
     type="password"
     placeholder="Paste your Claude API key"
     onChange={(e) => localStorage.setItem("anthropic_key", e.target.value.trim())}
   />
   ```

4. **Call the model from the browser.** In a `"use client"` component, inside an event handler (never at module top level, never server-side):
   ```jsx
   "use client"
   import Anthropic from "@anthropic-ai/sdk"

   export async function runRealFeature(userText) {
     const apiKey = typeof window !== "undefined" ? localStorage.getItem("anthropic_key") : null
     if (!apiKey) throw new Error("no-key")
     const client = new Anthropic({ apiKey, dangerouslyAllowBrowser: true }) // browser mode → CORS passes
     const res = await client.messages.create({
       model: "claude-opus-4-8",            // see note: a cheaper model can stretch a shared class budget
       max_tokens: 2048,
       messages: [{ role: "user", content: `…your prompt, including ${userText}…` }],
     })
     return res.content.find((b) => b.type === "text")?.text ?? ""
   }
   ```
   - **Model:** default `claude-opus-4-8`. For one shared, capped classroom key across many people, the facilitator may switch to a cheaper model (e.g. `claude-haiku-4-5`) to stretch the budget — their call, not a silent downgrade.
   - **Prompt the wedge precisely.** Put the participant's real intent in the prompt; if the feature needs structured data back (a list, fields), ask for JSON and `JSON.parse` it, or use the SDK's structured-output helper.

5. **Degrade gracefully — no key, bad key, or rate limit must not crash the page.** Wrap the call:
   ```jsx
   try {
     const out = await runRealFeature(input)
     // …render the real result
   } catch (err) {
     if (err.message === "no-key") /* show the mocked sample + a "paste a key to go live" prompt */
     else if (err.status === 401) /* "That key didn't work — check it and try again." */
     else if (err.status === 429) /* "Hit the rate limit — wait a moment and retry." */
     else /* fall back to the mocked sample */
   }
   ```
   Without a key the prototype still works on the **mock** (the S3/S4 behavior) — so a public visitor with no key, or after you revoke, sees the sample, not an error.

6. **Run + verify.** `npm run dev`, paste the key, trigger the feature, confirm a **real** response appears. Then remove the key and confirm the **fallback** shows. Both paths must work.

7. **Re-check the static build still works.** `npm run build` must still produce `out/` (the browser SDK call is client-side, so static export is unaffected). If it deployed via `/idea-to-prototype:deploy`, push to re-deploy.

8. **Commit — verify no key leaked.** Before committing, grep the diff for the key. `git add -A`, then **inspect**, then commit. The key must appear **nowhere** in source, `next.config`, or any committed file.

## Hard rules

- **The key is pasted at runtime and lives only in the browser.** NEVER hardcode it, put it in `next.config`, bake it into the build via an env var, or commit it. The repo is public.
- **Stay static.** No API routes, no server actions, no server-side secret. The call runs in the browser with `dangerouslyAllowBrowser: true`. (This is the one deliberate exception to build's "no runtime secrets" — the secret is the *user's own*, supplied at runtime, never ours and never stored.)
- **One feature.** Make the wedge real; leave everything else mocked. Don't rebuild the app.
- **Always keep the mock fallback** so the site works with no key (for public visitors and after the key is revoked).
- **Never log or transmit the key** anywhere except the Anthropic API.
- Tell the facilitator to use a **spend-capped, dedicated key** and **revoke it after** the workshop.
