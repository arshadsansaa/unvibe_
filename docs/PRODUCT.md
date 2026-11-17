# Unvibe — product

## One line

Unvibe turns a stream of technical articles into an ordered curriculum, makes you understand every build before code exists, and tracks how small builds add up to bigger projects you then write about.

## The problem it solves (for its first user)

- I read many AI and agent articles and implement almost none of them.
- I build with AI agents and don't fully understand what they produce or how the pieces fit together.
- My fundamentals and git habits are fading.

## The core loop

1. **Library** — articles sync automatically from freeCodeCamp tag RSS feeds (and later my own bookmarks). Each is triaged: `build`, `read`, `watch`, or `off_goal`, and tagged with the concepts it teaches and the concepts it needs first.
2. **Path** — a planner orders buildable articles into weekly paths of 5 steps toward my goal, and says *why* each step comes now and what it unlocks. New articles wait in an inbox; the path only changes at a week boundary.
3. **Read phase** — read the article. Code blocks are visible.
4. **Predict** — before any plan, I sketch which files and functions the build needs.
5. **Blueprint** — Claude Code, in plan mode, writes a blueprint in a fixed format: files, purpose, functions with signatures, owner (`me` or `agent`), connections, tests. Unvibe scores my prediction against it.
6. **Build** — in VS Code with Claude Code. Agent writes wiring, boilerplate and tests; I write functions marked `OWNER: me`. Article code is hidden; revealing it counts as a hint.
7. **Check** — on each commit a git hook runs `unvibe check`: tests, plus a **drift report** comparing the blueprint with the real code (via Python's `ast`). Unplanned functions get flagged.
8. **Explain-back** — 3 questions about my own code, graded against a rubric.
9. **Review and recall** — later: a planted bug to find in my solution; a blank-file rebuild of a core function.
10. **Capstone** — after 5 builds: one bigger project combining them, with one part that has no tutorial. Then I write a blog post about it myself.

## Principles

1. Generate, don't consume: every step ends with code I wrote or a decision I made.
2. Understanding over authorship: Unvibe can't see who typed a line, so it tests what I understand.
3. Blueprint before code, always.
4. Help fades but never disappears; every hint is logged honestly.
5. Local-first: my data, repos and articles stay on my machine.
6. Cheap by design: Unvibe's own LLM calls are small, cached and logged. Heavy code generation happens in Claude Code.
7. Respect the source: link to articles; never republish their text.

## Not in v1

Accounts, multi-user, a hosted version, payments, languages other than Python for builds, editor extensions, any feature that writes my `OWNER: me` functions for me.

## Success for me

- After 4 weeks: 20 verified builds, 4 capstones, 2 blog posts published.
- I can rebuild an agent loop with tools from a blank file in under 30 minutes.
- My blueprint prediction score rises week over week.
- I can explain every module of Unvibe itself.
