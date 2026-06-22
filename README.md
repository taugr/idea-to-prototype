# From Idea to Prototype — Workshop Plugin

A Claude Code and Codex plugin marketplace for the **From Idea to Prototype** workshop at **TUMO Labs**.

Over six sessions, participants take a raw idea all the way to a working prototype, using AI as a thinking partner, a designer, and a coding agent. This repo holds the custom Claude Code and Codex plugin that participants install for the workshop.

## What's in here

Two marketplace files listing the same plugin:

- `.claude-plugin/marketplace.json` for Claude Code
- `.agents/plugins/marketplace.json` for Codex

- **`idea-to-prototype`** — the workshop plugin. It contains:
  - **`ideation`** (Session 1): a rigorous AI thinking-partner that grills your idea for specificity and evidence, researches the landscape, narrows it to a buildable wedge, and writes a **Research Brief** you carry into the later sessions. It pushes for a *named* user and real demand evidence, and never writes code — its only output is the Brief.
  - **`setup-check`** (before Session 3): verifies your machine has the build tools — Node.js, npm, git, and a configured git identity — and guides installing anything missing. Read-only; never changes anything without asking.
  - **`build`** (Session 3): turns your Claude Design handoff into a running **Next.js** app configured for static export (GitHub-Pages-ready), porting the design faithfully and getting the first screen rendering.
  - **`deploy`** (Session 4): deploys your app to a live **Vercel** URL — signs in the Vercel CLI, deploys, sets any server-side env vars (like the AI key) and redeploys, and turns off Deployment Protection so the URL is public. Confirms before any public or account action.
  - **`add-ai`** (optional "go live"): makes **one** faked feature real with a small **server API route** on Vercel; the Claude key stays **server-side** as an environment variable — never in the browser or the repo. Always keeps a mock fallback so the app still works without a key.
  - **`pitch`** (Session 5): turns your working prototype into a **pitch deck + demo script** — a coached, four-step flow you direct: capture screenshots into a `pitch/` folder, outline the story, generate a `.pptx` with the screenshots embedded, and draft a 2-minute demo script. Pauses at each step for your review.

## Requirements

- [Claude Code](https://claude.com/claude-code)
- [Codex](https://developers.openai.com/codex)
- A paid Claude or ChatGPT plan with access to the tool you are using
- Web search enabled (the ideation skill researches the competitive landscape)
- *(For deploying in Session 4)* a free [Vercel account](https://vercel.com) — sign in with `npx vercel login` (your GitHub login works)
- *(Optional, only for the `add-ai` "go live" step)* an [Anthropic API key](https://console.anthropic.com), set as a server-side environment variable — use a dedicated, spend-capped key and revoke it after the workshop

## Install in Claude Code

```
/plugin marketplace add taugr/idea-to-prototype
/plugin install idea-to-prototype@tumo-ai-workshop
```

## Install in Codex

```
codex plugin marketplace add taugr/idea-to-prototype
codex plugin add idea-to-prototype@tumo-ai-workshop
```

After installing, start a new Codex thread so the bundled skill is available.

## Use in Claude Code

Start the Session 1 ideation skill:

```
/idea-to-prototype:ideation
```

## Use in Codex

Start a new thread and ask Codex to use the `ideation` skill from `idea-to-prototype`, or invoke the plugin explicitly from the Codex app composer.

Then describe your idea in one or two plain sentences — or say you don't have one yet. Bring a rough idea, not a polished pitch: the skill's job is to push you to sharpen it. When it finishes, you'll have a `research-brief.md` in your working folder.

## The workshop

1. **Idea Discovery & AI Research** — sharpen an idea into a Research Brief *(this plugin)*
2. **Design & Visualize** — turn the idea into screen mockups
3. **Build Sprint 1** — first working version
4. **Build Sprint 2** — core workflow
5. **Polish, Test & Pitch Prep**
6. **Demo Day**

Full session plans and class notes will be published in this repo at the end of the workshop.

## License / use

Workshop materials for TUMO Labs. 
