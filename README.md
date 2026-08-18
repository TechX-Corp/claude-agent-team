# Agent team for Claude Code

A full-SDLC agent team you get by cloning a repo. No install step, no global config —
Claude Code reads `.claude/` from the project root automatically.

## Use it

```bash
git clone <this-repo> my-product && cd my-product
rm -rf .git && git init          # start your own history
claude
```

then:

```
/build-product "a mood tracker for small teams"
```

## What runs

Nine phases. Phases 1-3 stop for your approval; 4-9 run through to a pull request.

| Phase | Agent | Artefact |
|---|---|---|
| 1 Discovery | `product-strategist` | `docs/validated-idea.md` |
| 2 Specs + UX *(parallel)* | `spec-writer`, `ux-designer` | `requirements.md`, `user-stories.md`, `ux-wireframes.md` |
| 3 Architecture | `technical-architect` | `architecture.md`, `implementation-plan.md`, **contract file + test** |
| 4 Scaffolding | `devops-engineer` | repo, CI, green empty suite |
| 5 Implementation *(parallel worktrees)* | `backend-developer`, `frontend-developer` | feature branches |
| 6 Tests | `test-engineer` | test suite |
| 7 Review + security *(parallel)* | `code-reviewer`, `security-auditor` | `review-report.md`, `security-audit.md` |
| 8 Ship | `devops-engineer` | pull request |
| 9 Feedback | `feedback-triage` | `docs/backlog.md` |

## The two ideas that make it work

**Artefacts are the handoff.** Each phase writes a file; the next phase reads it.
Nothing depends on what was said in chat, so the pipeline survives `/clear`, a crash,
or someone else picking it up next week.

**The contract is frozen at Phase 3.** The architect ships the interface as a real code
file with a test pinning it. Phase 5 implementers may read it but never edit it — which
is what lets backend and frontend run in parallel git worktrees without colliding.

## Existing project?

Run `/build-product` anyway. Phases whose artefact already exists are skipped, and the
orchestrator tells you which. Most existing projects enter at Phase 5.

## Adapting it

- `CLAUDE.md` — your stack, commands, conventions. Every agent reads it.
- `.claude/agents/*.md` — edit a role's rules, or add one.
- `.claude/commands/build-product.md` — change the phase order or drop phases.

Starter prompts inside each agent come from the
[Claude Code prompt library](https://code.claude.com/docs/en/prompt-library).
