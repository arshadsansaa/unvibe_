# Blueprint template

Every plan in `docs/plans/<plan-name>.md` uses this shape. The `yaml` block is machine-read by Unvibe (phase 4+), so keep its keys exactly as below. Prose sections are for the developer.

---

# <Plan title>

## Goal
One or two sentences: what will work after this change that doesn't work now.

## Out of scope
Bullets of things deliberately not done in this plan.

## How it connects
One arrow chain per flow, using real module and function names:
`cli.sync -> library.sync.run -> feeds.fetch -> feeds.parse_item -> db.upsert_article`

## Blueprint

```yaml
plan: phase-1-library-sync          # kebab-case, matches the file name
dependencies_added: [feedparser, httpx, selectolax]
files:
  - path: src/unvibe/library/feeds.py
    status: new                      # new | changed | unchanged-but-relevant
    purpose: Fetch tag feeds and turn entries into ArticleIn records.
    functions:
      - name: fetch
        signature: "fetch(url: str, client: httpx.Client) -> list[dict]"
        owner: agent                 # me | agent
        does: Download one feed and return raw entries.
      - name: parse_item
        signature: "parse_item(entry: dict) -> ArticleIn"
        owner: me
        does: Normalise one entry - guid, url, title, author, published_at (UTC), tags.
    called_by: [src/unvibe/library/sync.py:run]
    calls: [src/unvibe/db.py:upsert_article]
  - path: src/unvibe/db.py
    status: changed
    purpose: Add upsert for articles.
    functions:
      - name: upsert_article
        signature: "upsert_article(conn: sqlite3.Connection, a: ArticleIn) -> bool"
        owner: me
        does: Insert or update by guid; return True if the row is new.
    called_by: [src/unvibe/library/sync.py:run]
    calls: []
tests:
  - path: tests/test_feeds.py
    cases: [parses_fixture_feed, handles_missing_author, dates_are_utc]
  - path: tests/test_db.py
    cases: [upsert_inserts_new, upsert_updates_existing_no_duplicate]
```

## Risks and questions
Up to 3 bullets. Anything the developer must decide before approving.

---

## Rules for filling this in

- Every function that will exist after the change appears in `functions`, including private helpers.
- `owner: me` goes on the core logic the roadmap assigns to the developer for this phase. When unsure, mark `me` and say why in Risks.
- Tests for `owner: me` functions are written by the agent before the developer implements them.
- If implementation needs anything not listed here, stop and update this file first.
