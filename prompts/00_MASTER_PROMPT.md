# Master prompt — paste this first, in plan mode

Copy everything between the lines into Claude Code after switching to plan mode.

---

We're starting a new project called **Unvibe**. I'm building it to rebuild my own coding skills, so how we work matters as much as what we build.

**Step 1 — Read, don't write.**
Read `CLAUDE.md`, `docs/PRODUCT.md`, `docs/ARCHITECTURE.md`, `docs/ROADMAP.md` and `docs/BLUEPRINT_TEMPLATE.md`. Do not create or edit any files yet.

**Step 2 — Show me you understood.**
Reply with:
- What Unvibe is, in 3 sentences, in your own words.
- The rules from `CLAUDE.md` you'll follow, in 5 short bullets.
- Anything in the docs that is contradictory, vague or risky. Up to 5 points, most important first.
- Up to 3 questions you need answered before planning Phase 0 and Phase 1.

Then stop and wait for my answers.

**Step 3 — Ask for my prediction.**
After I answer, ask me to predict the files and functions for **Phase 0 + Phase 1** (see `docs/ROADMAP.md`). Wait for my prediction and save it to `docs/plans/phase-0-1.prediction.md` once you're allowed to write files.

**Step 4 — Blueprint.**
Write the blueprint for Phase 0 + Phase 1 to `docs/plans/phase-0-1.md` in the exact template format. Mark `owner: me` on the functions the roadmap assigns to me for Phase 1. Then show me:
- matched / missed / extra versus my prediction, one line each,
- the arrow chains for `unvibe init` and `unvibe sync`,
- the dependencies you want to add and why each is needed.

Stop and wait for "approved".

**Step 5 — Build in small commits.**
After approval, build Phase 0 first, then Phase 1, in small commits with clear messages. For every `owner: me` function: create the stub with a precise docstring, write its tests first, then hand it to me and wait. Don't move to the next `owner: me` function until I say mine is done and tests pass.

**Step 6 — Wrap up.**
When Phase 1's gate in `docs/ROADMAP.md` passes, give me the wiring summary from `CLAUDE.md` section 4, then ask me 3 explain-back questions about the functions I wrote. Don't give the answers; grade mine.

---

## Your answers to have ready

- **Goal:** e.g. "build AI agents from first principles without frameworks".
- **Feeds:** which freeCodeCamp tags to follow (see `config.example.toml`).
- **Model for Unvibe's own calls:** a small, cheap model; set it in `config.toml`.
