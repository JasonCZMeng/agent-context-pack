# agent-context-pack

**Persistent memory for AI agents, version-controlled and synced through GitHub.**

AI agents are stateless. Every new session, every new machine, every new agent starts from zero — no memory of what the project is, what decisions were made, or how things work. This skill solves that by giving every repo a structured context pack that acts as **durable, shared memory** any agent can read instantly.

Because it lives in git, this memory is:
- **Persistent** — survives across sessions, machines, and agent restarts
- **Versioned** — every change is tracked, reviewable, and reversible via PR
- **Portable** — any agent on any machine clones the repo and has full context immediately
- **Collaborative** — multiple agents and humans contribute to the same shared understanding
- **Authoritative** — one source of truth, not scattered across chat logs and local files

## The Problem

Without persistent context, every agent session is groundhog day:
- Agent doesn't know what the repo is for or what files matter
- Agent doesn't know what decisions were made or why
- Agent doesn't know what it's allowed to do
- Agent doesn't know domain-specific terminology
- Agent on Machine B has no idea what Agent on Machine A learned yesterday
- Onboarding a new agent (or a new model) means re-explaining everything from scratch

Chat history is ephemeral. Local memory files don't sync. The knowledge dies when the session ends.

## The Solution

Add two things to every repo:

1. **`CLAUDE.md`** at the root — the agent entry point
2. **`.context/`** directory — structured context pack: project metadata, permissions, onboarding, architecture, glossary, decisions, and workflows

Any agent that lands in the repo reads `CLAUDE.md`, follows the reading list into `.context/`, and within 30 seconds has full project understanding — who owns it, how it's built, what the terms mean, what decisions were made, and what it's allowed to do.

Because it's in git:
- Agents on different machines read the same context
- Context updates go through PRs (reviewed, approved, merged)
- Architecture decisions accumulate as ADRs over time
- The glossary grows as the project grows
- New agents or models inherit everything previous agents learned

**Your agents finally have a shared brain.**

## Use as a Template

Click **"Use this template"** on GitHub to scaffold a new repo with the context pack structure already in place.

## Install as a Skill

### Claude Code

```bash
gh repo clone JasonCZMeng/agent-context-pack
cp -r agent-context-pack/skill/workspace-context-pack ~/.claude/skills/workspace-context-pack
```

Claude Code auto-discovers it. When you set up a new repo or add context to an existing one, it uses this skill automatically.

### Other Agents

Point your agent at `skill/workspace-context-pack/SKILL.md` as a reference document. The templates in `context-pack-template.md` are copy-paste scaffolds.

## Quick Start (without installing)

- **[SKILL.md](skill/workspace-context-pack/SKILL.md)** — the full process, standards, and templates
- **[context-pack-template.md](skill/workspace-context-pack/context-pack-template.md)** — copy-paste scaffold for every file

## What's in the Context Pack?

```
your-repo/
  CLAUDE.md                           # Agent entry point — ordered reading list
  .context/
    project.yaml                      # Name, type, tech stack, key files
    permissions.yaml                  # What AI can/can't do
    context/
      README.md                       # Index of context files
      onboarding.md                   # What this repo is, first steps
      architecture.md                 # Repo structure, how pieces fit
      glossary.md                     # Domain-specific terms (grows over time)
      decisions/
        ADR-0001-template.md          # Architecture Decision Record template
      workflows/
        dev.md                        # Branch → PR → merge
        build.md                      # How to build/validate
        release.md                    # How to release/publish
  .github/
    pull_request_template.md          # Status packet + checklist
```

Each file serves a specific memory function:

| File | Memory Function |
|---|---|
| `project.yaml` | "What is this project and what matters most?" |
| `permissions.yaml` | "What am I allowed to do here?" |
| `onboarding.md` | "How do I get started?" |
| `architecture.md` | "How is this built and why?" |
| `glossary.md` | "What do these domain terms mean?" |
| `decisions/` | "What was decided and why?" (accumulates over time) |
| `workflows/` | "How do I develop, build, and release?" |

## Multi-Repo Workspace (Optional)

For teams or individuals with multiple repos, add a registry repo with:

- **`workspace.yaml`** — manifest listing all repos (one source of truth)
- **`bootstrap.sh`** — clones/pulls all repos on any new machine
- **`doctor.sh`** — verifies the toolchain is installed

Any new machine goes from zero to fully synced in 3 commands. See SKILL.md for details.

## License

MIT
