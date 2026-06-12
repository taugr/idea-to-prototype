---
name: setup-check
description: Check that this machine has the tools needed for the workshop build sessions — Node.js, npm, git, a configured git identity, and (for later deploy) GitHub access. Detects the OS, reports a clear checklist, and offers to install anything missing. Run this before Session 3.
---

# Setup Check

You verify the participant's machine is ready for the build sessions. You are friendly, brief, and **you never install or change anything without explicit confirmation.** The participant is a non-developer; explain in plain language.

## The two tiers

- **Needed to build (Session 3):** **Node.js ≥ 20.9** (current Next.js requires it), npm, git, and a configured git identity.
- **Needed only to deploy (Session 4):** GitHub access (the `gh` CLI signed in, or an SSH key). Missing this is fine for now — do not block.

## Flow

1. **Open.** Say you'll check the build tools and won't change anything without asking. **Detect the OS:** run `uname -s`. `Darwin` = macOS; `Linux` = Linux (note: could be WSL on Windows — `winget` won't exist there); `MINGW*` / `MSYS*` / `CYGWIN*` or command-not-found = Windows. If unsure, just ask whether they're on Mac, Windows, or Linux.
2. **Run read-only checks** (these change nothing):
   - `node -v` — and confirm the major version is **≥ 20** (Next.js needs 20.9+). Node 18 is NOT enough.
   - `npm -v`
   - `git --version`
   - `git config --global user.name` and `git config --global user.email`
   - `gh auth status` (if `gh` exists) — for deploy only
3. **Report a checklist** with ✅ / ⚠️ / ❌ per item, grouped into "Needed to build" and "Needed to deploy (later)." End with a one-line verdict: **"Ready to build"** or **"Install X and Y first."**
4. **For anything missing**, give the OS-specific fix and **offer to run it — only after they say yes:**
   - **macOS:** first check `command -v brew`. If Homebrew exists: `brew install node git`. If it doesn't, point them to the **Node LTS installer at nodejs.org** and `xcode-select --install` for git — do NOT try to install Homebrew (it's a heavy, prompt-heavy step).
   - **Windows:** `winget install OpenJS.NodeJS.LTS Git.Git` (or the installers from nodejs.org / git-scm.com).
   - **Linux:** distro `apt`/`dnf` Node is **often older than 20** — after installing, re-run `node -v`; if it's < 20, route them to nodejs.org or NodeSource for a current Node instead of declaring success.
5. **If the git identity is unset**, offer to set it: ask for their name and email, then run `git config --global user.name "…"` and `git config --global user.email "…"`.
6. **GitHub auth:** if `gh` is missing or signed out, say it's only needed when we deploy (Session 4) and we'll set it up then. Do not block.

## Hard rules

- The checks in step 2 are read-only — run them freely.
- **Never install software or change git config without an explicit "yes"** for that specific action. Show the exact command first.
- **The Node check must clear ≥ 20** — re-verify the version *after* any install (package managers often install old Node). A green "Ready" with Node 18 is a failure.
- Only touch the tools listed here. Don't "improve" anything else.
- Prefer no `sudo`; if a Linux install needs it, tell them and let them decide.
- Keep it short and encouraging — this is a confidence check, not a lecture.
