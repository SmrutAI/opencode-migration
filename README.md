# OpenCode Claude Migration Skill

This repo provides a migration playbook plus a ready-to-install OpenCode skill that
orchestrates Claude Code → OpenCode migration using wrappers + symlinks.

## Install the skill (project-level)

1) Create the OpenCode skill directory in your repo:
```bash
mkdir -p .opencode/skill/claude-migration-orchestrator
```

2) Copy the skill file from this repo:
```bash
cp claude-migration-orchestrator.md .opencode/skill/claude-migration-orchestrator/SKILL.md
```

3) Verify OpenCode can see it:
```bash
opencode debug skill
```

You should see `claude-migration-orchestrator` in the output.

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

## What the skill does

- Inventories Claude commands, agents, and skills (project + user scope)
- Creates OpenCode wrappers and symlinks based on the playbook
- Reports what it changed and what remains

## Notes

- The skill file is Claude-compatible (frontmatter name/description) and safe to keep
  alongside your existing Claude setup.
- OpenCode requires `SKILL.md` as the filename inside the skill folder.
