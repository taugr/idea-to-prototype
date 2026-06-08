# From Idea to Prototype — Workshop Plugin

A Claude Code and Codex plugin marketplace for the **From Idea to Prototype** workshop at **TUMO Labs**.

Over six sessions, participants take a raw idea all the way to a working prototype, using AI as a thinking partner, a designer, and a coding agent. This repo holds the custom Claude Code and Codex plugin that participants install for the workshop.

## What's in here

Two marketplace files listing the same plugin:

- `.claude-plugin/marketplace.json` for Claude Code
- `.agents/plugins/marketplace.json` for Codex

- **`idea-to-prototype`** — the workshop plugin. It currently contains:
  - **`ideation`** skill (Session 1): a rigorous AI thinking-partner that grills your idea for specificity and evidence, researches the landscape, narrows it to a buildable wedge, and writes a **Research Brief** you carry into the later sessions. It pushes for a *named* user and real demand evidence, and never writes code — its only output is the Brief.

## Requirements

- [Claude Code](https://claude.com/claude-code)
- [Codex](https://developers.openai.com/codex)
- A paid Claude or ChatGPT plan with access to the tool you are using
- Web search enabled (the ideation skill researches the competitive landscape)

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
