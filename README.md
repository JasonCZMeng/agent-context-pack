# workspace-context-pack

A reusable AI skill and template for setting up **PR-first, multi-repo workspaces** with standardized context packs that make any repo instantly understandable to AI agents.

## The Problem

You drop an AI agent into a repo and it has no idea:
- What the repo is for
- What files matter most
- What it's allowed to do
- How the project is structured
- What domain terms mean

## The Solution

Add two things to every repo:

1. **`CLAUDE.md`** at the root — the agent entry point
2. **`.context/`** directory — structured context pack with project metadata, permissions, onboarding, architecture, glossary, and workflows

Any agent that lands in the repo reads `CLAUDE.md` first, which points it to the context pack. Within 30 seconds it understands the repo.

## Install as a Skill

### Claude Code

Copy the `skill/` directory into your Claude Code skills path:

```bash
cp -r skill/workspace-context-pack ~/.claude/skills/workspace-context-pack
```

Claude Code will auto-discover it. When you ask it to set up a new repo or add context to an existing one, it will use this skill.

### Other Agents

Point your agent at `skill/workspace-context-pack/SKILL.md` as a reference document. The templates in `skill/workspace-context-pack/context-pack-template.md` can be used as copy-paste scaffolds.

## Quick Start (without installing)

If you just want the templates, see:

- **[SKILL.md](skill/workspace-context-pack/SKILL.md)** — the full process and standards
- **[context-pack-template.md](skill/workspace-context-pack/context-pack-template.md)** — copy-paste scaffold for every file you need

## What's in the Context Pack?

```
your-repo/
  CLAUDE.md                           # Agent entry point
  .context/
    project.yaml                      # Name, type, tech stack, key files
    permissions.yaml                  # What AI can/can't do
    context/
      README.md                       # Index of context files
      onboarding.md                   # What this repo is, first steps
      architecture.md                 # Repo structure, how pieces fit
      glossary.md                     # Domain-specific terms
      decisions/
        ADR-0001-template.md          # Architecture Decision Record template
      workflows/
        dev.md                        # Branch → PR → merge
        build.md                      # How to build/validate
        release.md                    # How to release/publish
  .github/
    pull_request_template.md          # Status packet + checklist
```

## Multi-Repo Workspace (Optional)

If you manage multiple repos, you can also set up:

- **`workspace.yaml`** — manifest listing all repos
- **`bootstrap.sh`** — script that clones/pulls all repos from the manifest
- **`doctor.sh`** — script that checks required tools are installed

See the SKILL.md for details on workspace-level setup.

## License

MIT
