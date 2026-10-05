# agent-starter

A vendor-neutral **team engineering foundation for humans + AI coding agents**.
It contains no application code. Use it as a GitHub template (or copy it into an
existing repository) so developers using Claude Code, Codex, Cursor, Kilo Code,
Kiro, Windsurf, Copilot, or future tools can work on the same codebase under one
shared contract.

## Why vendor-neutral
Tools change; teams mix them. Per-tool rule files drift apart and contradict each
other, and AI chat history is invisible to teammates. This repo fixes that with:

- **One contract**: `AGENTS.md` is the canonical instruction file. Do not copy it into
  `CLAUDE.md`, `.cursorrules`, etc. Codex, Cursor, Copilot, Kilo, Windsurf and others read
  `AGENTS.md` natively or can be pointed to it; Claude Code can use a one-line pointer file
  if needed (see [`.ai/README.md`](.ai/README.md)).
- **Repository = memory.** Conversation history is temporary and local. Anything the team needs
  later goes in Git (docs, ADRs, issues, PRs).
- **GitHub = coordination.** Agents and people collaborate via issues, branches, PRs, ADRs, and test results.

| Role | Responsibility |
|---|---|
| AI agent | Implementation assistant |
| Developer | Owner of the change |
| Reviewer | Independent quality gate |
| Repository | Durable shared knowledge |
| AI conversation | Temporary working context |

## Quick Start for a New Project
1. Click **Use this template** on GitHub (or copy these files into your repo, never overwriting existing ones).
2. Edit `.ai/project.json`: set `project.*`, `technology.*`, and **only real, verified** `commands.*`
   (leave `null` if unknown). Then set `"templateMode": false`.
3. Run `python scripts/ai/validate_governance.py` (needs only Python 3).
4. Copy `.github/CODEOWNERS.example` to `.github/CODEOWNERS` with real owners.
5. Fill in `SECURITY.md` (contact) and `docs/architecture/README.md`.
6. Complete the GitHub checklist below.
7. Commit through a PR; the `AI Governance` workflow must pass.

## Repository structure
```
AGENTS.md            canonical agent contract (<= 200 lines)
CONTRIBUTING.md      human + AI contribution workflow
SECURITY.md          vulnerability reporting
.ai/                 project.json, policies/, workflows/, templates/
docs/                architecture/, decisions/ (ADRs), development/
scripts/ai/          validate_governance.py (stdlib only)
.github/             PR + issue templates, CODEOWNERS.example, governance CI
```
Existing project files are never required to move; add governance alongside them.

## Git workflow
One task = one issue + one branch + (optionally) one worktree + one primary agent per worktree.
Branches: `feature|fix|refactor|docs|chore/<issue-id>-<short-name>`. Conventional Commits.
`main` is protected: PR required, no direct or force pushes. Full rules: [`.ai/policies/git.md`](.ai/policies/git.md).

## Worktree workflow
```
git worktree add ../myrepo-123 -b feature/123-auth origin/main   # Dev A + Claude Code
git worktree add ../myrepo-124 -b feature/124-api  origin/main   # Dev B + Codex
git worktree add ../myrepo-125 -b fix/125-ui       origin/main   # Dev C + Cursor
git worktree remove ../myrepo-123                                # after merge
```
Never run two agents in one worktree, and never modify another person's worktree.

## AI-agent workflow
Understand -> Plan (TRIVIAL / STANDARD / HIGH RISK) -> Implement -> Verify -> Review -> Report.
Details: [`AGENTS.md`](AGENTS.md) and [`.ai/workflows/`](.ai/workflows/). Task context goes in the
**AI-assisted task** issue template so any teammate or agent can pick it up.

## Human approval boundaries
Agents stop and ask before destructive data operations, production changes, real credentials,
auth/security-control changes, irreversible migrations, force pushes/history rewrites, repo security
settings, broader CI permissions, bypassing failing checks, or out-of-scope changes.
The full list is in `AGENTS.md` section 13.

## Customizing
- **Project rules**: put universal rules in `AGENTS.md` only if they apply to every task; otherwise add a file under
  `.ai/policies/` or `.ai/workflows/` and link it from `AGENTS.md`. Keep `AGENTS.md` <= 200 lines.
- **Nested `AGENTS.md`**: for large repos add `backend/AGENTS.md`, `frontend/AGENTS.md`, `infrastructure/AGENTS.md`, etc.
  They contain **only additional rules for that subtree**; never copy the root policy. Closest file wins, but they can never
  weaken security or Git safety rules.
- **ADRs**: copy `.ai/templates/adr.md` to `docs/decisions/NNNN-short-title.md`, set status, add it to the index
  in `docs/decisions/README.md`, and get it reviewed.
- **Tool adapters**: only if a tool cannot read `AGENTS.md`. Make a thin, labeled, reviewed pointer; no duplicated policy.
- **Legacy codebases**: adopt by adding these files alongside existing ones; do not rewrite, modernize, or re-tool.

## GitHub protection and security checklist
These are repository settings that this template cannot apply for you. Configure them under
**Settings** (or via an authenticated admin); do not assume they are on:

- [ ] Default branch is `main`
- [ ] Branch protection / ruleset on `main`: require PR, >= 1 approving review, require status check
      `validate-governance` (job in `AI Governance`), block force pushes, block deletion
- [ ] Require review from code owners (after creating `.github/CODEOWNERS`)
- [ ] Secret scanning and push protection enabled
- [ ] Dependabot alerts / security updates (where appropriate for your stack)
- [ ] Private vulnerability reporting enabled
- [ ] Default workflow token permissions set to read-only

## What must NEVER be committed
Secrets, API keys, tokens, passwords, certificates, `.env` files, cloud/SSH credentials, AI chat
transcripts or session dumps, hidden agent memory, local MCP/IDE/agent configuration, private local paths,
personal preferences, or customer/business-sensitive data. See [`SECURITY.md`](SECURITY.md).
