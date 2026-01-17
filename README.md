# OpenCode Claude Migration Skill

This repo provides a migration playbook plus a ready-to-install OpenCode skill that
orchestrates Claude Code → OpenCode migration using wrappers + symlinks.


## Install the skill (user-level)

1) Create the global OpenCode skill directory:
```bash
mkdir -p ~/.config/opencode/skill/claude-migration-orchestrator
```

2) Copy the skill file:
```bash
cp claude-migration-orchestrator.md ~/.config/opencode/skill/claude-migration-orchestrator/SKILL.md
```

3) Verify:
```bash
opencode debug skill
```

## Playbook

The full migration playbook is in:
- `claude_migration_playbook.md`

## Prompt
Use a prompt like:

```text
Use the claude-migration-orchestrator skill.
Scope:
- Project-level: this repo
- User-level: ~/.claude
Requirements:
- Migrate all Claude commands, agents, and skills.
- Use wrappers + symlinks (do NOT edit Claude files).
- Keep subfolder (.claude/commands/<group>) commands under .opencode/command/<group>/
- All Claude agents should become subagents in OpenCode. (except ... any agent you need to be primary, that sits with "Plan" and "Build" agents of opencode)
- Report a checklist of what you found and what you created.
Proceed.
```

## What the skill does

- Inventories Claude commands, agents, and skills (project + user scope)
- Creates OpenCode wrappers and symlinks based on the playbook
- Reports what it changed and what remains

