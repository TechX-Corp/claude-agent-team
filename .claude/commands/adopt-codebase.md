---
description: Adopt an existing codebase into the agent-team workflow — map what is really there, backfill the missing docs/ artefacts from the code, then hand off to /build-product at the right phase.
argument-hint: [optional: what you want to build next]
---

# /adopt-codebase

Next goal (optional): **$ARGUMENTS**

For a repo that already has code but no complete `docs/` artefacts — the common case.
`/build-product` assumes a blank folder; this fills the gap so it can resume mid-pipeline.

**The code is the source of truth, not the docs, not the tickets, not what anyone remembers.**
Every artefact you write here is reverse-engineered from what actually runs. Where you cannot
find the answer in the code, write `UNKNOWN — needs owner` rather than a plausible guess.
An invented rationale is worse than an admitted gap: it closes the question.

---

## Step 1 — Map what exists
`Agent(Explore)` or `Agent(general-purpose)`. Do not read everything into this context;
have the subagent absorb the sprawl and return a summary.

Produce `docs/codebase-map.md`:
- entry points — what starts, and how (the real command, verified by running it)
- directory → responsibility, one line each
- data stores, external services, background jobs
- how it deploys today
- **install / run / test commands, each actually executed.** A README command that has
  never been run is fiction; mark it BROKEN and move on rather than fixing it here.

Done when: `test -f docs/codebase-map.md` and every command in it is marked WORKS or BROKEN.

## Step 2 — Find the seams
Where are the boundaries a parallel track could form along? Look for:
- an existing types/schema/API module → this may already be your contract
- modules with no imports between them → candidate independent tracks
- one file everything routes through → a bottleneck, and NOT a split point

**First check what is already written.** `ls docs/` and look for the same content under
another name — `ARCHITECTURE.md`, a `wiki/`, a `design/` folder. If one exists, write your
delta into that file. Only create `docs/architecture.md` when nothing corresponds.

Describe the system **as built**, clearly labelled as-built, not as-designed.
Two labels, never mixed: what exists, and what is intended.

## Step 3 — Backfill only what the next goal needs
Do **not** reverse-engineer nine phases of paperwork for a working system. That is
ceremony, and nobody will read it.

Given the goal above, write only the missing artefacts it depends on:

| Next goal | Backfill |
|---|---|
| New feature | `user-stories.md` for that feature + the contract it touches |
| Refactor | `architecture.md` as-built + the tests that pin current behaviour |
| Redesign | `ux-wireframes.md` — inventory **every** existing screen first |
| Bug/incident work | `backlog.md` via `Agent(feedback-triage)` |
| Just organising | stop after Step 2. That was the ask. |

Done when: every artefact named in the row above exists on disk, or is listed in Step 5
as deliberately skipped with a reason.

## Step 4 — Freeze a contract, if you are about to parallelise
Only if the next goal splits into independent tracks.
`Agent(technical-architect)`: promote the existing types/schema module into the explicit
contract, **add a test that pins it**, and record which tracks may touch which files.
No test pinning it means it is not frozen — it is just a file people happen to agree on.

## Step 5 — Report the entry point, then stop
State plainly:
- what you found, and what you could not determine
- which artefacts you wrote, which you deliberately skipped and why
- **which `/build-product` phase this project enters at** (most land at Phase 5)
- any command in the README that does not work

Do not name a phase you cannot back with a file: `ls docs/` is the evidence for this report.

**CHECKPOINT — and the only one.** Steps 1-4 run straight through without stopping to ask;
questions raised along the way are collected and asked here, once. Do not start implementing.
The human decides what happens next.
