# Unvibe — starter kit

Unvibe turns the articles you read into a structured curriculum you actually build.
This kit sets up a repo so you can build Unvibe **with Claude Code, without vibecoding it**.

## What's in here

| File | What it's for | Who reads it |
|---|---|---|
| `CLAUDE.md` | The rules Claude Code follows in every session | Claude Code (auto-loaded) |
| `docs/PRODUCT.md` | What Unvibe is and isn't | You + Claude Code |
| `docs/ARCHITECTURE.md` | Modules, data model, how pieces connect | You + Claude Code |
| `docs/ROADMAP.md` | 8 phases, each with a gate and the functions **you** write | You + Claude Code |
| `docs/BLUEPRINT_TEMPLATE.md` | The exact format every plan must follow | Claude Code |
| `docs/plans/` | Where blueprints and your predictions are saved | Both |
| `prompts/00_MASTER_PROMPT.md` | The first prompt you paste into Claude Code | You |
| `prompts/PHASE_PROMPTS.md` | Prompts for every later phase and session | You |
| `.claude/settings.json` | Saves plan-mode plans into `docs/plans/` | Claude Code |
| `.claude/commands/*.md` | `/blueprint`, `/hint`, `/explain`, `/wrap` shortcuts | Claude Code |
| `config.example.toml` | Feeds, goal, model settings | Unvibe |

## Start in 10 minutes

1. Create an empty folder and copy this kit into it.
2. `git init && git add . && git commit -m "Unvibe starter kit"`
3. Install [uv](https://docs.astral.sh/uv/) and Python 3.11+.
4. Copy `config.example.toml` to `config.toml`. Put your Anthropic API key in an environment variable (`ANTHROPIC_API_KEY`), never in the file.
5. Open the folder in VS Code, start Claude Code, switch to **plan mode** (Shift+Tab until it says plan mode, or type `/plan`).
6. Paste `prompts/00_MASTER_PROMPT.md`.
7. When it asks, **write your own prediction first**. Then read its blueprint, approve, and build.

## The rules you're signing up for

- **Blueprint before code.** Every change starts as a plan in `docs/plans/` in the template format.
- **Predict before you look.** You sketch the files and functions in 2 minutes before reading Claude Code's plan.
- **Functions marked `OWNER: me` are yours.** Claude Code writes setup, wiring, boilerplate and tests. You write the core logic.
- **Stuck? Use `/hint`.** One level at a time: nudge, approach, pseudocode, code. Every hint is logged in the commit.
- **Finish with `/explain`.** Three questions about your own code before a phase counts as done.

You're dogfooding Unvibe while you build it: for the first phases you follow these rules by hand. Each phase then automates one of them.

## Checking things that change

Claude Code features change often. If a setting or command in `.claude/` doesn't behave as described, check the [Claude Code docs](https://docs.claude.com/en/docs/claude-code/overview) and adjust. The freeCodeCamp feed slugs in `config.example.toml` are guesses from the site's tags; open each feed URL once to confirm it works.
