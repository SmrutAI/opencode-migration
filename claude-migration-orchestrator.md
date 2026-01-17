---
name: claude-migration-orchestrator
description: Analyze Claude Code commands, agents, and skills at user/project scope and apply the OpenCode migration playbook via wrappers and symlinks
compatibility: opencode
metadata:
  audience: maintainers
  scope: migration
---
## What I do
- Inventory Claude Code commands, agents, and skills at both project and user levels
- Compare against OpenCode discovery paths
- Create wrappers or symlinks following the migration playbook
- Report what was changed and what remains

## When to use me
Use this skill when you want OpenCode to reuse existing Claude Code assets without manual setup.
Ask for confirmation before making filesystem changes.

## Playbook reference
@/Users/tushar/.opencode/claude_migration_playbook.md

## Inputs I expect
- Repo root path
- Whether to operate project-level, user-level, or both
- Whether to use wrappers vs direct edits

## Outputs I produce
- A checklist of assets discovered
- A list of wrappers/symlinks created
- Any follow-up actions needed
