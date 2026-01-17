# OpenCode + Claude Code Migration Playbook

This is a step-by-step tutorial for integrating OpenCode into an existing Claude Code setup
without duplicating your commands, agents, or skills. Use this as a practical checklist.

## 0) Goal and mental model
- Claude Code assets live in `.claude/` and `~/.claude/`
- OpenCode discovers skills/agents/commands under `.opencode/` and `~/.config/opencode/`
- We will **wrap** or **symlink** Claude assets so OpenCode can reuse them

## 1) Inventory your Claude setup
1. List your project-level Claude assets:
   - `.claude/commands/`
   - `.claude/skills/`
2. List your user-level Claude assets:
   - `~/.claude/commands/`
   - `~/.claude/skills/`
3. Decide which items should be shared with OpenCode (usually all of them).

## 2) Create OpenCode project scaffolding
1. In your repo root, create `.opencode/` if missing.
2. Add a skills symlink:
   ```bash
   ln -s .claude/skills .opencode/skill
   ```
3. Add an agents directory (if you plan to add wrappers locally):
   ```bash
   mkdir -p .opencode/agent
   ```
4. Add a commands directory (for wrapper commands):
   ```bash
   mkdir -p .opencode/command
   ```

## 3) Create OpenCode user scaffolding
1. Ensure the global config folder exists:
   ```bash
   mkdir -p ~/.config/opencode
   ```
2. Add a global skills symlink:
   ```bash
   ln -s ~/.claude/skills ~/.config/opencode/skill
   ```
3. Add global agents/commands folders:
   ```bash
   mkdir -p ~/.config/opencode/agent
   mkdir -p ~/.config/opencode/command
   ```

## 4) Skills: share Claude skills without duplication
- OpenCode supports Claude-compatible skill discovery via:
  - `.claude/skills/<name>/SKILL.md`
  - `~/.claude/skills/<name>/SKILL.md`
- By symlinking `.opencode/skill` to `.claude/skills` (and global equivalents),
  OpenCode discovers the exact same skills.
- Keep Claude as the source of truth for skills.

## 5) Agents: wrap Claude prompts as OpenCode agents
OpenCode agents can reference prompt files using `prompt: "{file:...}"`.
This lets you reuse existing Claude agent prompts.

### Option A: Project-level wrapper agent
Create `.opencode/agent/review.md`:
```markdown
---
description: Claude-style code reviewer
mode: subagent
model: anthropic/claude-sonnet-4-20250514
prompt: "{file:./.claude/agents/review.md}"
tools:
  write: false
  edit: false
---
You are in review mode. Provide feedback only.
```

### Option B: User-level wrapper agent
Create `~/.config/opencode/agent/review.md`:
```markdown
---
description: Claude-style code reviewer
mode: subagent
prompt: "{file:~/.claude/agents/review.md}"
---
```

## 6) Commands: wrap Claude commands with required frontmatter
OpenCode command files require frontmatter (at minimum `description`).
Claude commands usually lack frontmatter, so we wrap them.

### Example wrapper (project-level)
Create `.opencode/command/enforce-code-disciplines.md`:
```markdown
---
description: Enforce code discipline checklist
agent: plan
---
@.claude/commands/enforce-code-disciplines.md
```

### Example wrapper (global)
Create `~/.config/opencode/command/prime-architecture.md`:
```markdown
---
description: Load architecture context
agent: plan
---
@~/.claude/commands/priming/architecture.md
```

## 7) Permissions: align OpenCode behavior with Claude workflows
OpenCode can restrict tools per agent or per command. Examples:
- Set a Plan agent to `ask` on edits and bash
- Deny `write/edit` for review agents
- Allow only safe bash commands

Example (opencode.json):
```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "plan": {
      "permission": {
        "edit": "ask",
        "bash": "ask"
      }
    }
  }
}
```

## 8) Verify discovery in OpenCode
1. Start OpenCode in the repo.
2. Check skills:
   - Use the `skill` tool list (skills should appear).
3. Check commands:
   - Type `/` and verify custom commands show in autocomplete.
4. Check agents:
   - Type `@` and verify wrapper agents appear.

## 9) Keep a single source of truth
- Skills: **Claude is canonical**; OpenCode reads via symlink.
- Commands: **Claude is canonical**, OpenCode uses wrappers with frontmatter.
- Agents: **Claude prompts are canonical**, OpenCode wrappers reference those files.

## 10) Optional: automation for command wrappers
If you have many Claude commands, you can script wrapper generation:
- Enumerate `.claude/commands/**/*.md`
- Create a matching file in `.opencode/command/`
- Add frontmatter + `@` reference

Pseudo-script idea:
```bash
for cmd in $(find .claude/commands -name "*.md"); do
  name=$(basename "$cmd" .md)
  out=".opencode/command/$name.md"
  printf "---\ndescription: Claude command %s\n---\n@%s\n" "$name" "$cmd" > "$out"
done
```

## 11) Troubleshooting checklist
- Skill not found: verify `SKILL.md` frontmatter has `name` + `description`.
- Command not listed: ensure wrapper file has frontmatter.
- Agent not listed: ensure the wrapper file exists in `.opencode/agent/` or `~/.config/opencode/agent/`.
- Permission errors: loosen `permission` in `opencode.json` or in agent frontmatter.

## 12) Minimal example for sharing everything
```bash
# Project
mkdir -p .opencode/agent .opencode/command
ln -s .claude/skills .opencode/skill

# Global
mkdir -p ~/.config/opencode/agent ~/.config/opencode/command
ln -s ~/.claude/skills ~/.config/opencode/skill
```

That’s it. With wrappers + symlinks, OpenCode and Claude Code can share the same
skills, agents, and command content while each tool stays happy with its own format.
