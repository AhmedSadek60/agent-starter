# agent-starter

A vendor-neutral starting point for teams where developers use different AI
coding agents (Claude Code, Codex, Cursor, Kilo Code, Kiro, Windsurf, Copilot,
and whatever comes next) on the same codebase. It holds rules, workflows and
templates only. There is no application code.

## Author

Ahmed Sadek

## How it works

All agents follow one file, `AGENTS.md`. Do not copy it into `CLAUDE.md`,
`.cursorrules` or similar. Detail that is not needed for every task lives in
`.ai/` and `docs/`, and `AGENTS.md` links to it.

| Idea | Rule |
| --- | --- |
| One contract | `AGENTS.md` is the only instruction file. It stays at 200 lines or fewer. |
| Repository is memory | Chat history is temporary and local. Anything the team needs later goes in Git. |
| GitHub is coordination | People and agents work through issues, branches, PRs, ADRs and test results. |
| One task | One issue, one branch, optionally one worktree, one primary agent per worktree. |

| Role | Responsibility |
| --- | --- |
| AI agent | Implementation assistant |
| Developer | Owner of the change |
| Reviewer | Independent quality gate |

## Layout

| Path | Contents |
| --- | --- |
| `AGENTS.md` | The canonical agent contract. |
| `.ai/project.json` | Stack and commands for this project (metadata only). |
| `.ai/policies/` | Security, git, testing, change control, documentation, collaboration. |
| `.ai/workflows/` | Task execution, features, bugs, refactoring, code review, incidents. |
| `.ai/templates/` | Implementation plan, ADR, handoff, investigation. |
| `docs/` | `architecture/`, `decisions/` (ADRs), `development/`. |
| `scripts/ai/validate_governance.py` | Checks the files above. Python 3 standard library only. |
| `.github/` | PR and issue templates, `CODEOWNERS.example`, governance workflow. |
| `CONTRIBUTING.md`, `SECURITY.md` | Contribution workflow and vulnerability reporting. |

### Setup

Click **Use this template**, or copy these files into an existing repository
without overwriting anything.

1. Edit `.ai/project.json`. Fill in the project and technology fields and only
   commands you have run. Leave unknown commands as `null`.
2. Set `"templateMode": false`.
3. Copy `.github/CODEOWNERS.example` to `.github/CODEOWNERS` and use real owners.
4. Add a security contact to `SECURITY.md`.
5. Describe the architecture in `docs/architecture/README.md`.

### Check the files

```
python scripts/ai/validate_governance.py
```

It prints errors and exits with code 1 when something is wrong, otherwise 0.
The same check runs in CI as `validate-governance`. It never runs commands from
`project.json`. Your project's own build and test pipeline is separate.

### Git workflow

Branch names are `feature|fix|refactor|docs|chore/<issue-id>-<short-name>`.
Commits follow Conventional Commits. `main` takes changes through PRs only, with
no direct or force pushes. An agent never merges its own work unless a human
explicitly tells it to. Details are in `.ai/policies/git.md`.

For parallel work give each task its own worktree:

```
git worktree add ../myrepo-123 -b feature/123-auth origin/main
git worktree add ../myrepo-124 -b feature/124-api  origin/main
git worktree remove ../myrepo-123      # after the PR is merged
```

Never run two agents in one worktree or touch another person's worktree.

### How agents work

Understand, plan, implement, verify, review the diff, report. Tasks are
classified as trivial, standard or high risk. High risk work needs a written
plan and a human approval before anything irreversible. Put task context in the
**AI-assisted task** issue template so another person or agent can pick it up
without your chat. See `AGENTS.md` and `.ai/workflows/`.

Agents must stop and ask first for: destructive data operations, production
changes, real credentials, auth or security-control changes, irreversible
migrations, force pushes, repository security settings, wider CI permissions,
bypassing failing checks, and anything outside the task. The full list is in
`AGENTS.md`.

### Customizing

| To do this | Do this |
| --- | --- |
| Add a project rule | If it applies to every task, add it to `AGENTS.md`. Otherwise add a file under `.ai/policies/` or `.ai/workflows/` and link it. |
| Add rules for one folder | Add a nested `AGENTS.md` (for example `backend/AGENTS.md`) with only the extra rules for that folder. Never copy the root file. It cannot weaken security or git rules. |
| Record a decision | Copy `.ai/templates/adr.md` to `docs/decisions/NNNN-short-title.md`, add it to the index there, get it reviewed. |
| Support a tool that ignores `AGENTS.md` | Add a thin adapter (for example `.cursor/rules/`) that points to `AGENTS.md`, is labeled vendor-specific, and is reviewed like code. The validator rejects adapters over 15 lines. |
| Adopt in a legacy repo | Add these files alongside the existing ones. Do not re-tool or modernize unrelated code. |

Changes to `AGENTS.md`, `SECURITY.md`, `CONTRIBUTING.md`, `.ai/policies/` and
the governance workflow need review by another team member.

### GitHub settings

These are repository settings that files cannot apply. Set them yourself under
**Settings** and do not assume they are on.

- [ ] Default branch is `main`.
- [ ] Branch protection on `main`: PR required, at least one approval, required
      check `validate-governance`, no force pushes, no deletion.
- [ ] Code owner review required (after creating `.github/CODEOWNERS`).
- [ ] Secret scanning and push protection on.
- [ ] Dependabot alerts and security updates, if they fit your stack.
- [ ] Private vulnerability reporting on.
- [ ] Default workflow token permissions set to read-only.

### Never commit

Secrets, API keys, tokens, passwords, certificates, `.env` files, cloud or SSH
credentials, AI chat transcripts or session dumps, local MCP, IDE or agent
configuration, private local paths, personal preferences, and customer or
business data. `.gitignore` covers the common cases.

### Troubleshooting

| Symptom | Cause and fix |
| --- | --- |
| `AGENTS.md has N lines (limit 200)` | Move detail into `.ai/` or `docs/` and link to it. |
| `vendor-specific policy file does not reference AGENTS.md` | A `CLAUDE.md`, `.cursorrules` or similar file exists. Reduce it to a short pointer to `AGENTS.md`, or delete it. |
| `templateMode is false but project.name is still TBD` | Fill in the project fields in `.ai/project.json`. |
| `CODEOWNERS still contains placeholder owners` | Replace the `@ORG/...` handles with real users or teams. |
| `possible secret detected` | Remove the value from the file and revoke the credential. Deleting it from history alone is not enough. |
| `broken relative link` | The linked file moved or does not exist. Fix the path. |
