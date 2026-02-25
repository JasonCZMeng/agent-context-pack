# Context Pack Template

Copy-paste scaffold for adding a context pack to any repo. Replace all `<placeholders>` with repo-specific values.

---

## File: `CLAUDE.md` (repo root)

```markdown
# <Repo Name> — Agent Instructions

## Before You Do Anything

Read these files in order to understand this repo:

1. `.context/project.yaml` — what this repo is, its tech stack, and which files matter most
2. `.context/permissions.yaml` — what you're allowed to do
3. `.context/context/onboarding.md` — how to get started
4. `.context/context/architecture.md` — repo structure and how the pieces fit together
5. `.context/context/glossary.md` — key terms used in this project

## What This Repo Is

<One paragraph describing the repo's purpose and what it contains.>

## Permissions

You may read any file in this repo. **Do not write, create, or delete files directly.** All changes must go through a pull request and require human approval. See `.context/permissions.yaml`.

## Workflow Rules

- All changes go through PRs — see `.context/context/workflows/dev.md`
- Use the PR template at `.github/pull_request_template.md`

## Key Files to Read First

<Organize by task type. Example:>

When asked about <topic-a>:
- `path/to/relevant-file.md`

When asked about <topic-b>:
- `path/to/other-file.md`

## Architecture Decisions

Check `.context/context/decisions/` for ADRs. Use the template at `ADR-0001-template.md` when proposing new decisions.
```

---

## File: `.context/project.yaml`

```yaml
name: <repo-name>
type: <app|api|library|tooling|meta|infra>
description: <one-line description>
owner: <github-username>
created: <YYYY-MM-DD>
tech_stack:
  - <language>
  - <framework>
ai_context:
  primary_files:
    - <most-important-file>
  ignore_patterns:
    - "<glob-to-skip>"
```

---

## File: `.context/permissions.yaml`

```yaml
ai_permissions:
  can_read: true
  can_write: false
  can_create_files: false
  can_delete_files: false
  requires_approval:
    - all_writes
notes: "AI agents should read context but never write directly. All changes via PR."
```

---

## File: `.context/context/README.md`

```markdown
# <repo-name> Context Pack

This directory provides structured context for AI assistants working with this repo.

## Files
- `onboarding.md` - What this repo is and how to get started
- `architecture.md` - System design and repo structure
- `glossary.md` - Key terms and definitions
- `decisions/` - Architecture Decision Records (ADRs)
- `workflows/` - Dev, build, and release workflows
```

---

## File: `.context/context/onboarding.md`

```markdown
# Onboarding: <repo-name>

## What is this?
<2-3 sentences describing the repo and what it contains.>

## First Steps
1. <First thing to do>
2. <Second thing to do>
3. <Third thing to do>
```

---

## File: `.context/context/architecture.md`

```markdown
# Architecture: <repo-name>

## Role
<What role does this repo play? What does it NOT contain?>

## Repo Layout
<ASCII tree of top-level directories with one-line descriptions>
```

---

## File: `.context/context/glossary.md`

```markdown
# Glossary

- **<Term>** - <Definition an AI wouldn't already know>
```

---

## File: `.context/context/decisions/ADR-0001-template.md`

```markdown
# ADR-0001: [Title]

## Status
[Proposed | Accepted | Deprecated | Superseded]

## Context
[What is the issue or decision we need to make?]

## Decision
[What did we decide?]

## Consequences
[What are the positive and negative outcomes?]
```

---

## File: `.context/context/workflows/dev.md`

```markdown
# Development Workflow

1. Create a branch from `main` (e.g., `feature/thing`, `fix/bug`)
2. Make changes locally
3. Commit with descriptive messages
4. Push branch and open a PR
5. Review the PR (or have AI review it)
6. Merge via squash-and-merge
7. Delete the branch after merge
```

---

## File: `.context/context/workflows/build.md`

```markdown
# Build Workflow

<How to build or validate this repo. If no build step, say so.>
```

---

## File: `.context/context/workflows/release.md`

```markdown
# Release Workflow

<How to release or publish. If no releases, say so.>
```

---

## File: `.github/pull_request_template.md`

```markdown
# Summary

## Status Packet
- Objective:
- What changed:
- How to test:
- Risks / gotchas:
- Next steps:
- Links:

## Checklist
- [ ] No secrets committed
- [ ] Context pack updated
- [ ] Validation steps included
```

---

## Directory creation command

```bash
mkdir -p .context/context/decisions .context/context/workflows .github
```
