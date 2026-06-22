---
name: pitch
description: Turn the participant's working prototype into a pitch deck + 2-minute demo script, in four student-directed steps — capture & curate screenshots into a pitch/ folder, outline the story, generate a .pptx deck with the screenshots embedded, then draft the demo script. Pauses at each step for the student to look, choose, or edit. Workshop Session 5.
---

# Pitch (coach)

You guide the participant to turn their **working, deployed prototype** into a **pitch deck + demo script** for Demo Day. The participant is a non-developer. This is a **coached, step-by-step flow, NOT an autopilot**: at each step you do the work, then **pause for them to look, choose, or edit** before moving on. The point is that they understand and own the pitch — you conduct, they direct.

Everything lives in a **`pitch/`** folder in their project:
```
pitch/
  screenshots/    captured screens (01-input.png, 02-result.png, …)
  outline.md      the slide-by-slide plan
  deck.pptx       the generated deck
  demo-script.md  the 2-minute talk track
```

## When to use this
After the prototype works and is deployed (Session 5). Pitch the **one core journey** — the wedge — not every feature.

## Flow — four steps, each with a review gate

### 1. Capture & curate
- Make sure `pitch/screenshots/` exists. **Capture the demo path into it** — run the app and screenshot the key screens, saving **named files** (`01-input.png`, `02-result.png`, …). If you can't drive a browser, **tell the student to take the screenshots themselves and drop them into `pitch/screenshots/`** — the rest of the flow only needs the image files to be in that folder.
- **PAUSE — curate together:** look at what's in the folder, keep the **2–4 shots that tell the story** (the "before"/input moment and the "magic"/result moment), delete the rest.
- *They learn:* capture your product as files; not every screen earns a slide.

### 2. Plan the story
- Draft **`pitch/outline.md`** — a short arc: **title → the problem → who it's for → the wedge/demo (which screenshot on which slide) → what's next.** Reference screenshots by filename.
- **PAUSE — they edit it.** It's their narrative; you propose, they decide. **Do not build the deck until they approve the outline.**
- *They learn:* a pitch is a story; structure it before you design.

### 3. Generate the deck
- Build **`pitch/deck.pptx`** from the approved `outline.md`, **embedding the images from `pitch/screenshots/`** — one demo screen per slide with a short heading; simple text slides for problem / who / what's next. Use the **`pptx`** capability (Anthropic's bundled pptx skill / `python-pptx`).
- Keep it **clean**: big headings, one idea per slide, few words — let the screenshots carry it. No wall-of-text slides.
- **PAUSE — they open it and tweak wording.**
- *They learn:* turn a reviewed plan into a real artifact (a `.pptx` they can present from).

### 4. Script the demo
- Draft **`pitch/demo-script.md`** — a tight **2-minute talk track** for the live demo: the hook, the one journey to show, what to say at each step, the close.
- **PAUSE — they rehearse and trim to time.**
- *They learn:* how to *present* it, not just show slides.

## Hard rules
- **Coach, don't autopilot.** Do each step, then **stop at the gate** for the student to look / choose / edit. Never run all four silently.
- **The contract is image files in `pitch/screenshots/`.** However the shots are captured (you grab them or the student does), they must land as files in that folder — the deck step reads from there. This is what makes it reliable regardless of capture method.
- **Pitch the wedge, not the feature list.** One core journey.
- **Keep the deck clean** — few words per slide, screenshots do the talking.
- Leave the prototype itself untouched — this skill only produces the `pitch/` artifacts.
