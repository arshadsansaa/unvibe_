# Unvibe — roadmap

Eight phases. Each ends with a **gate**: don't start the next phase until it passes.
Each phase lists the functions **you** write (`owner: me`). Everything else is fair game for Claude Code.

## Phase 0 · Skeleton (half a day)

Build: `pyproject.toml` with `uv`, the `unvibe` CLI entry point, `config.py`, `db.py` with migrations from `schema.sql`, `tests/fakes.py`, ruff and pytest set up. `unvibe init` creates the database.

Yours: nothing to write yet. Read every file Claude Code creates and be able to say what each one is for.

Gate: `uv run unvibe init` creates the database; `uv run pytest` passes; you can explain the folder structure without looking.

## Phase 1 · Library sync

Build: `unvibe sync` fetches the configured tag feeds, extracts text and code blocks, stores new articles, and marks them `inbox`. `unvibe library` lists them.

Yours:
- `feeds.parse_item(entry) -> ArticleIn` — turn one RSS entry into a clean record (dates, author, tags).
- `extract.split_code_blocks(html) -> tuple[str, list[CodeBlock]]` — prose text and code blocks separately.
- `db.upsert_article(conn, article) -> bool` — insert or update by guid; return whether it was new. Write the SQL yourself.

Gate: two syncs in a row add no duplicates; tests pass using saved feed fixtures; no network in tests.

## Phase 2 · Triage and tagging

Build: `unvibe triage` classifies new articles and tags concepts through `llm/client.py`, validated by Pydantic, cached, cost-logged.

Yours:
- `triage/schemas.py` — the Pydantic models (you design the shape).
- `triage/prompts.py` — the triage prompt, version `v1`.
- `tagger.normalize_concepts(raw: list[str]) -> list[str]` — merge near-duplicates ("tool calling" / "function calling").

Gate: on 20 real articles, you agree with 18+ triage labels; a re-run costs nothing (cache hits).

## Phase 3 · Concept graph and path planner

Build: `unvibe goal "<text>"`, `unvibe path`, `unvibe why <step>`.

Yours:
- `graph.build(conn) -> ConceptGraph`
- `planner.plan_week(graph, goal, done) -> WeekPlan` — choose 5 buildable articles whose `needs` are covered by what you've done or earlier steps; order them; explain each choice in one sentence. **This is the heart of Unvibe.**

Gate: the planner reproduces a sensible week 1 (loop → tools → safe loop → memory → evals) from your real library, and tests cover a cycle in the graph and a goal with no buildable articles.

## Phase 4 · Link, predict, blueprint

Build: `unvibe link <repo>` (installs a `CLAUDE.md` section, `docs/plans/`, the git hook), `unvibe predict`, `unvibe blueprint` (parse and score the latest plan).

Yours:
- `blueprint.parse(md_text) -> Blueprint` — extract and validate the yaml block.
- `blueprint.score_prediction(pred, bp) -> PredictionScore` — matched, missed, extra.

Gate: on a real plan file, the score matches what you'd count by hand.

## Phase 5 · Check and drift

Build: the post-commit hook and `unvibe check`: run tests, index functions with `ast`, compare with the approved blueprint, read `Unvibe-Hint` trailers from the commit.

Yours:
- `ast_index.functions_in(path) -> list[FunctionInfo]`
- `drift.compare(bp, indexed, changed_files) -> DriftReport` — planned/built, planned/missing, unplanned, files touched outside the plan.

Gate: an unplanned function in a commit is flagged; a planned one renamed shows as missing plus unplanned.

## Phase 6 · Explain-back, review, recall

Build: `unvibe explain`, `unvibe review`, `unvibe recall`, `unvibe due`.

Yours:
- `schedule.next_due(history) -> date` — your spaced-repetition rule (start simple: 3, 7, 21 days, reset on failure).
- The explain-back rubric format in `learn/explain.py`.

Gate: grades on 10 of your own answers match your own judgment at least 8 times.

## Phase 7 · Local web UI

Build: FastAPI + Jinja2 + HTMX, matching the canvas screens: Home, Library, Path, Read, Blueprint, Build tracker, Explain, Review, Capstone. Reads the same SQLite database. No frontend build step.

Yours: the Blueprint page's compare view (template + the route that feeds it).

Gate: you use the web UI, not the CLI, for a full week.

## Phase 8 · Capstone generator and MCP server

Build: `unvibe capstone` generates a brief from the week's builds plus a blog outline. A small MCP server lets Claude Code ask Unvibe for the current step, its objectives, and which functions are `owner: me`.

Yours: the MCP tool definitions (you learn MCP by building it, which is week 2 of your own path).

Gate: Claude Code, in a learning repo, can answer "what am I building and which functions are mine?" by calling Unvibe.

## Your personal checkpoints

- End of phase 3: write a short post on how you designed the planner.
- End of phase 5: rebuild `drift.compare` from a blank file in 30 minutes.
- End of phase 8: blog post "I built the tool that taught me to stop vibecoding."
