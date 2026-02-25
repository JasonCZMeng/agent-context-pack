---
name: workspace-context-pack
description: Use when setting up a new multi-repo workspace, adding a repo to a workspace, bootstrapping a new machine, or scaffolding a project with a standardized AI context pack. Also use when asked about workspace structure, context packs, or making repos agent-readable.
---

# Workspace Context Pack

## Overview

A repeatable process for making any GitHub repo instantly understandable to AI agents. Every repo gets a `.context/` directory and a `CLAUDE.md` so any agent landing in the repo can self-orient immediately.

**Core principle:** An agent should understand any repo within 30 seconds of landing in it.

## When to Use

- Making an existing repo agent-readable
- Setting up a new multi-repo workspace from scratch
- Adding a new repo to an existing workspace
- Bootstrapping a new machine to work with the workspace
- Creating or updating context packs

**When NOT to use:**
- Restructuring an existing repo's internal code (just edit normally)
- One-off scripts that don't need agent context

## Quick Reference

| Component | Purpose | Location |
|---|---|---|
| `CLAUDE.md` | Agent entry point | Every repo root |
| `.context/` | AI context pack | Every repo root |
| `workspace.yaml` | Lists all repos (multi-repo only) | Registry/control repo |
| `bootstrap.sh` | Clones/pulls all repos (multi-repo only) | Registry/control repo |
| `.github/pull_request_template.md` | PR status packet | Every repo |

## The Context Pack Standard

Every repo MUST contain these at the root:

### `CLAUDE.md` (agent entry point)

The first thing any agent reads. Must contain:
1. Ordered reading list pointing into `.context/`
2. What the repo is (one paragraph)
3. Permissions (what the agent can and cannot do)
4. Workflow rules (branch → PR → squash merge)
5. "Key Files to Read First" organized by task type
6. Code standards (if applicable)

### `.context/` directory

```
.context/
  project.yaml          # Name, type, tech stack, primary files, ignore patterns
  permissions.yaml      # AI read/write permissions
  context/
    README.md           # Index of context files
    onboarding.md       # What this repo is, first steps
    architecture.md     # Repo structure, how pieces fit
    glossary.md         # Domain-specific terms
    decisions/
      ADR-0001-template.md
    workflows/
      dev.md            # Branch → PR → merge
      build.md          # How to build/validate
      release.md        # How to release/publish
```

### `.github/pull_request_template.md`

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

## Adding Context to a Repo

1. **Create a branch:**
   ```bash
   git checkout -b scaffold/add-context-pack
   ```

2. **Scaffold files** — use the context pack template (see `context-pack-template.md`)

3. **Write `CLAUDE.md`** — customize the ordered reading list and key files for this repo's domain

4. **Commit, push, PR, merge:**
   ```bash
   git add -A
   git commit -m "scaffold: add AI context pack"
   git push -u origin scaffold/add-context-pack
   gh pr create --title "scaffold: add AI context pack" --body "..."
   gh pr merge --squash --delete-branch
   ```

## Multi-Repo Workspace Setup

For workspaces with multiple repos, create a **registry repo** that contains:

### `workspace.yaml`

```yaml
workspace_root: ~/workspace/repos
repos:
  - name: my-registry
    url: git@github.com:YOUR_ORG/my-registry.git
  - name: my-app
    url: git@github.com:YOUR_ORG/my-app.git
  - name: my-tools
    url: git@github.com:YOUR_ORG/my-tools.git
```

### `bootstrap.sh`

A script that reads `workspace.yaml` and clones/pulls all repos:

```bash
#!/usr/bin/env bash
set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
WORKSPACE_YAML="$SCRIPT_DIR/../workspace.yaml"

WORKSPACE_ROOT=$(grep 'workspace_root:' "$WORKSPACE_YAML" | awk '{print $2}')
WORKSPACE_ROOT="${WORKSPACE_ROOT/#\~/$HOME}"
mkdir -p "$WORKSPACE_ROOT"

grep '^\s*- name:' "$WORKSPACE_YAML" | awk '{print $3}' | while read -r repo_name; do
  repo_dir="$WORKSPACE_ROOT/$repo_name"
  if [ -d "$repo_dir/.git" ]; then
    echo "[$repo_name] Pulling latest..."
    git -C "$repo_dir" pull --ff-only || echo "  WARNING: pull failed (uncommitted changes?)"
  else
    echo "[$repo_name] Cloning..."
    gh repo clone "YOUR_ORG/$repo_name" "$repo_dir" || echo "  WARNING: clone failed"
  fi
done
```

### `doctor.sh`

A script that checks required tools are installed:

```bash
#!/usr/bin/env bash
set -euo pipefail

PASS=0
FAIL=0

check() {
  local name="$1" cmd="$2" hint="$3"
  if command -v "$cmd" &>/dev/null; then
    echo "  [OK] $name: $("$cmd" --version 2>&1 | head -1)"
    PASS=$((PASS + 1))
  else
    echo "  [MISSING] $name — $hint"
    FAIL=$((FAIL + 1))
  fi
}

echo "Checking tools..."
check "git"    "git"    "https://git-scm.com/"
check "gh"     "gh"     "https://cli.github.com/"
check "node"   "node"   "https://nodejs.org/"
check "python" "python" "https://python.org/"
# Add your own: check "rojo" "rojo" "https://rojo.space/"

echo "Results: $PASS passed, $FAIL missing"
[ "$FAIL" -gt 0 ] && exit 1 || echo "All checks passed!"
```

Any new machine gets working in 3 commands:
```bash
gh repo clone YOUR_ORG/my-registry ~/workspace/repos/my-registry
~/workspace/repos/my-registry/scripts/bootstrap.sh
~/workspace/repos/my-tools/scripts/doctor.sh
```

## `project.yaml` Template

```yaml
name: <repo-name>
type: <app|api|library|tooling|meta|infra>
description: <one line>
owner: <github-username>
created: <YYYY-MM-DD>
tech_stack:
  - <language>
  - <framework>
ai_context:
  primary_files:
    - <most-important-file>
    - <second-most-important>
  ignore_patterns:
    - "<glob-to-skip>"
```

## `permissions.yaml` Template

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

## Workspace Rules

These are non-negotiable across all repos:

1. **PR-first** — never commit to `main` directly
2. **No secrets in git** — no `.env`, API keys, credentials
3. **No destructive actions** without explicit user instruction
4. **Every repo has `.context/`** and `CLAUDE.md`
5. **Status packet in every PR** — objective, what changed, how to test, risks, next steps

## Common Mistakes

| Mistake | Fix |
|---|---|
| Creating `.context/` but no `CLAUDE.md` | Agents won't discover the context pack without `CLAUDE.md` at root |
| Putting secrets in config files | Use `.example.yaml` templates; gitignore actual configs |
| Committing directly to main | Always branch → PR → squash merge |
| Skipping `project.yaml` primary_files | Agents won't know which files matter most |
| Writing vague glossary entries | Include domain terms an AI wouldn't already know |
| Generic `CLAUDE.md` across all repos | Each repo needs its own key files and domain context |
| `((VAR++))` in bash with `set -e` | Use `VAR=$((VAR + 1))` — `((0++))` is falsy and kills the script |
