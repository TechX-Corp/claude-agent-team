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

## AWS projects

Seven agents carry an **AWS** section pointing at the official `aws-core` / `aws-agents`
plugin skills. Those plugins live in your Claude Code install, not in this repo — if they
are not installed the sections are simply ignored, and everything else still works.

| Agent | AWS skills it reaches for |
|---|---|
| `product-strategist` | `aws-billing-and-cost-management`, `agents-pay` — is the unit cost survivable |
| `technical-architect` | `aws-blocks`, `aws-serverless`, `aws-compute`, `aws-containers`, `aws-database`, `amazon-bedrock`, `agents-build`, billing |
| `backend-developer` | `aws-sdk-python-usage` / `aws-sdk-js-v3-usage`, `aws-secrets-manager`, `aws-database`, `aws-messaging-and-streaming`, `amazon-bedrock`, `agents-connect` |
| `devops-engineer` | `signing-in-to-aws`, `aws-cdk`, `aws-cloudformation`, `aws-deployment`, `launch-with-aws`, `aws-containers`, `aws-observability`, `agents-deploy` |
| `test-engineer` | `aws-observability` |
| `security-auditor` | `aws-iam`, `aws-secrets-manager`, `agents-harden` |
| `feedback-triage` | `aws-observability`, `agents-debug`, `agents-optimize`, billing |

Install them with `/plugin` if you do not have them. Not building on AWS? Delete the
AWS section from each agent — nothing else depends on it.

## Existing project?

Run `/build-product` anyway. Phases whose artefact already exists are skipped, and the
orchestrator tells you which. Most existing projects enter at Phase 5.

## Adapting it

- `CLAUDE.md` — your stack, commands, conventions. Every agent reads it.
- `.claude/agents/*.md` — edit a role's rules, or add one.
- `.claude/commands/build-product.md` — change the phase order or drop phases.

Starter prompts inside each agent come from the
[Claude Code prompt library](https://code.claude.com/docs/en/prompt-library).
