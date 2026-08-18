# Agent team — working rules

This repo carries a full-SDLC agent team in `.claude/`. Clone it, open Claude Code,
run `/build-product "<your idea>"`. Nothing to install.

## Commands
- `/build-product "<idea>"` — new product, 9 phases
- `/adopt-codebase ["<goal>"]` — existing code, no docs: map it, backfill, resume mid-pipeline

## How the team works
Eleven agents in `.claude/agents/`, one per SDLC role, driven by `/build-product`
through 9 phases. **Artefacts in `docs/` are the handoff between phases** — a later
phase reads the file rather than relying on conversation memory. That is what lets
the pipeline survive a `/clear`, a crash, or a new person picking it up tomorrow.

## Rules every agent follows
- **Verify before claiming done.** Run the command, paste the output. "Should work" is not a status.
- **Acceptance criteria are runnable commands**, not prose, wherever they can be.
- **Never merge your own PR.** Open it, report the URL, stop. A human merges.
- **Never push to main.** Branch, PR.
- **Minimum code that solves the problem.** No abstraction with one implementation, no config for a value that never changes, no error handling for impossible states.
- **Surgical changes.** Every changed line traces to a requirement. Notice unrelated dead code → mention it, do not delete it.
- **The interface contract is frozen** once Phase 3 approves it. Implementers read it; they never edit it. This is what makes parallel worktrees safe.
- **Ask when genuinely ambiguous**, in multiple-choice form. Do not silently pick between readings.

## Design handoffs
`.claude/skills/claude-design-handoff/` ships in this repo and works on any clone.
Use it for anything from claude.ai/design. Always `list_files` before building, and always
render-gate the result — screenshot it and compare, never claim a screen is done unseen.

## AWS
Seven agents have an **AWS** section listing the `aws-core` / `aws-agents` plugin skills
for their role. Consult them before choosing a service, writing SDK code, or deploying.
If the plugins are not installed, skip the section — do not improvise AWS advice from memory.

## Phase checkpoints
Phases 1-3 stop for approval. Phases 4-9 run without asking — being made to type
"continue" is an orchestration failure, not politeness.

## Adapting this for your project
Add your stack, commands, and conventions below. Agents read this file on every run.

<!-- PROJECT-SPECIFIC — fill this in
## Stack
## Commands
- install:
- run:
- test:
## Conventions
-->
