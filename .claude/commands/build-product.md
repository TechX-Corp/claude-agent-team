---
description: Drive an idea from discovery to a shipped PR through 9 SDLC phases. Checkpoints at phases 1-3, then self-drives.
argument-hint: <one-line product idea>
---

# /build-product

Idea: **$ARGUMENTS**

Drive the 9 phases below in order. Each phase writes its artefact into `docs/` —
**those files are the handoff between phases, not chat memory.** A later phase reads
the file; it does not rely on remembering.

## Rules that hold for every phase
- Phases 1-3 end at a checkpoint: show the artefact, ask **approve / edit / redo**, then STOP and wait.
- Phases 4-9 self-drive. Do not ask "shall I continue?" between them — finish the lane.
- Stop only at real gates: `gh pr create`, push to main, deleting things, spending money, exposing secrets.
- **Never merge your own PR.** Surface it and stop.
- If an artefact already exists (existing project), read it and SKIP that phase. Report which you skipped.
- Verify before claiming done: run the command, paste the output.

---

### Phase 1 — Discovery → `docs/validated-idea.md`
`Agent(product-strategist)`. One question at a time.
**CHECKPOINT.**

### Phase 2 — Specs + UX → `docs/requirements.md`, `docs/user-stories.md`, `docs/ux-wireframes.md`
Spawn BOTH in one message so they run concurrently:
`Agent(spec-writer)` and `Agent(ux-designer)`.
**CHECKPOINT.**

### Phase 3 — Architecture → `docs/architecture.md`, `docs/implementation-plan.md`, contract file + test
`Agent(technical-architect)`.
Must produce the **frozen interface contract as a real code file with a test pinning it**,
plus the **parallelisation map** (which tracks touch disjoint files).
**CHECKPOINT — the last one.** After this, do not ask again until Phase 9.

### Phase 4 — Scaffolding
`Agent(devops-engineer)`, **building the stack `docs/architecture.md` chose** — not a
substitute. Done = fresh clone installs, runs, and tests green. Prove all three.

### Phase 5 — Parallel implementation
For each track in the parallelisation map:
```bash
git worktree add ../$(basename "$PWD")-<track> -b feature/<track>
```
Spawn one implementer per track **in a single message** so they run concurrently —
`Agent(backend-developer)` / `Agent(frontend-developer)`, each with `isolation: worktree`.
Every one of them is told: **the contract file is frozen, do not edit it.**

### Phase 6 — Tests
`Agent(test-engineer)`. Mutation-check each new test: break the code, prove it goes red, restore.

### Phase 7 — Review + security → `docs/review-report.md`, `docs/security-audit.md`
Spawn BOTH in one message: `Agent(code-reviewer)` and `Agent(security-auditor)`.
Fix what is real. Decline nits with a reason; do not apply feedback wholesale.

### Phase 8 — Ship
`Agent(devops-engineer)`: deploy per `docs/architecture.md`, run the acceptance commands,
show output, then `gh pr create`.
**HARD GATE — stop.** Report the PR URL. Do not merge.

### Phase 9 — Feedback → `docs/backlog.md`
`Agent(feedback-triage)`. Split deterministic defects from quality complaints. Group by root cause.
