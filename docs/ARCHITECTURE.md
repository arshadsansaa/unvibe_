# Unvibe — architecture

Unvibe is a local Python app: a CLI first, a small local web UI later. It owns planning and checking. VS Code + Claude Code own building. Git and a few files connect them.

## How the pieces connect

```
 freeCodeCamp tag RSS feeds
        │  (daily: unvibe sync)
        ▼
 library.sync ──► library.extract ──► db.articles
        │
        ▼  (unvibe triage)
 triage.tagger ──► llm.client ──► db.triage, db.article_concepts
        │
        ▼  (unvibe path)
 path.graph ──► path.planner ──► db.paths, db.path_steps
        │
        ▼  (you read, then: unvibe predict)
 projects.predict ──► docs/plans/<plan>.prediction.md
        │
        ▼  (Claude Code plan mode writes docs/plans/<plan>.md)
 projects.blueprint (parse yaml) ──► compare with prediction ──► db.blueprints
        │
        ▼  (you + Claude Code build in the linked repo; git commit)
 git post-commit hook ──► unvibe check
        │
        ▼
 check.tests + check.ast_index + check.drift ──► db.checks
        │
        ▼
 learn.explain ──► learn.schedule (review, recall) ──► capstone ──► back to path.planner
```

Two kinds of repos are involved:

- **The Unvibe repo** — this one.
- **Learning repos** — where you build each week's projects (e.g. `~/code/agent-lab`). `unvibe link <path>` installs a `CLAUDE.md` section, a `docs/plans/` folder and a git post-commit hook there.

## Folder structure

```
unvibe/
├── CLAUDE.md
├── config.toml                # local, git-ignored (copy from config.example.toml)
├── pyproject.toml
├── docs/  prompts/  .claude/
├── src/unvibe/
│   ├── cli.py                 # Typer app; thin, no logic
│   ├── config.py              # load config.toml
│   ├── db.py                  # connection, migrations, small query helpers
│   ├── schema.sql             # all tables
│   ├── llm/
│   │   └── client.py          # the ONLY place that calls the Anthropic API
│   ├── library/
│   │   ├── feeds.py           # fetch + parse RSS
│   │   ├── extract.py         # HTML -> clean text, code blocks kept separate
│   │   └── sync.py            # orchestrates a sync, fills the inbox
│   ├── triage/
│   │   ├── schemas.py         # Pydantic models for LLM output
│   │   ├── prompts.py         # versioned prompts
│   │   └── tagger.py          # triage + concept tagging
│   ├── path/
│   │   ├── graph.py           # concept graph from article_concepts
│   │   └── planner.py         # weekly path of 5 + "why"
│   ├── projects/
│   │   ├── link.py            # install hook + CLAUDE.md section in a learning repo
│   │   ├── predict.py         # capture your prediction
│   │   └── blueprint.py       # parse plan yaml, compare with prediction
│   ├── check/
│   │   ├── tests.py           # run pytest, parse results
│   │   ├── ast_index.py       # functions/classes in a file via ast
│   │   ├── drift.py           # blueprint vs code
│   │   └── hook.py            # what the git hook runs
│   ├── learn/
│   │   ├── explain.py         # explain-back questions + grading
│   │   └── schedule.py        # spaced review and recall
│   ├── capstone/
│   │   └── generate.py        # capstone brief + blog outline
│   ├── web/                   # phase 7: FastAPI + Jinja2 + HTMX
│   └── mcp/                   # phase 8: MCP server for Claude Code
└── tests/
    ├── fakes.py               # FakeLLM with scripted responses
    └── fixtures/              # saved RSS, HTML, plan files
```

## Data model (SQLite)

| Table | Key columns | Purpose |
|---|---|---|
| `articles` | id, guid UNIQUE, url, title, author, published_at, feed_tags, text, code_blocks_json, content_hash, synced_at, inbox BOOL | Every synced article |
| `triage` | article_id, kind (`build`/`read`/`watch`/`off_goal`), reason, prompt_version | Triage result |
| `concepts` | id, name UNIQUE | Concept vocabulary |
| `article_concepts` | article_id, concept_id, role (`teaches`/`needs`) | Edges for the concept graph |
| `goals` | id, text, active BOOL | Your learning goal |
| `paths` | id, goal_id, week_start, status | One week's path |
| `path_steps` | path_id, position, article_id, why, unlocks, status | The 5 steps |
| `projects` | id, repo_path, path_step_id | A linked learning repo for a step |
| `blueprints` | id, project_id, plan_path, parsed_json, approved_at | Parsed plan |
| `predictions` | blueprint_id, text, parsed_json, score_json | Your guess and its score |
| `checks` | id, project_id, commit_sha, tests_passed, tests_total, drift_json, hints_json, ran_at | One `unvibe check` run |
| `explains` | id, project_id, question, answer, score, feedback | Explain-back |
| `schedule` | id, project_id, kind (`review`/`recall`), function, due_at, interval_days, result | Spaced repetition |
| `capstones` | id, path_id, brief_md, status | Capstone per week |
| `llm_calls` | id, purpose, model, tokens_in, tokens_out, cost_usd, cache_hit, created_at | Cost tracking |

## Key decisions

- **Feeds, not scraping.** freeCodeCamp exposes per-tag RSS at `https://www.freecodecamp.org/news/tag/<slug>/rss/`, including the full post HTML in `content:encoded`, so no page scraping is needed.
- **Graph decides order, LLM only tags.** The LLM labels what an article teaches and needs. Ordering is deterministic code (topological order + goal scoring) that you write and can test.
- **Drift needs no LLM.** Compare blueprint function names and files with what `ast` finds in the changed files of the commit.
- **Cheap model for Unvibe's own calls.** Triage, tagging and explain-back grading use a small, inexpensive model set in `config.toml`, with caching. Expect a few cents per week.
- **Plans live in files.** Claude Code's plan mode is configured to save plans in `docs/plans/`; Unvibe reads them from there.
- **Web UI later.** The CLI is the product until phase 7; the web UI reads the same database.
