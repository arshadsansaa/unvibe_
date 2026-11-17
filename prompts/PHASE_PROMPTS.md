# Phase and session prompts

## Start of every session

```
New session. Re-read CLAUDE.md and the latest plan in docs/plans/.
Tell me in 5 lines: where we are in the roadmap, which owner: me functions
are still stubs, what's failing, and the next smallest step. Don't write code yet.
```

## Starting any phase (plan mode)

Replace `N` and the name.

```
Plan Phase N (<name>) from docs/ROADMAP.md.
First ask me for my prediction of files and functions, and wait.
Then write docs/plans/phase-N-<name>.md in the template format,
compare it with my prediction (matched / missed / extra),
list new dependencies with one reason each, and stop for approval.
```

## When implementation wants to go off-plan

```
Stop. Show me exactly what you want to add that isn't in the blueprint and why.
Update docs/plans/<plan>.md first. I'll approve or reject.
```

## When I've finished an owner: me function

```
I've implemented <function>. Run its tests. If they fail, tell me which case
and point to the line, but don't fix my code. If they pass, review it like a
senior engineer in 3 bullets max: correctness, edge cases, readability.
```

## End of a phase

```
Phase N gate check. Run lint and tests, check the gate in docs/ROADMAP.md,
and report pass or fail per gate item. Then give me the wiring summary
(CLAUDE.md section 4) and 3 explain-back questions about my functions.
Grade my answers; don't give them away first.
```

## When I want to understand existing code

```
Walk me through <module> as if onboarding me: what it's for, the main
functions in call order, what calls it and what it calls. Then ask me
2 questions to check I got it. No code changes.
```

## Weekly retro (Sunday)

```
Read the git log for this week and docs/plans/. Summarise in 6 bullets:
what I built, which functions were mine, how many hints I used and at
what levels, what drifted from plans, and one thing to practise next week.
```
