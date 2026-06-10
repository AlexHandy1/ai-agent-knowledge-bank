# Learnings & Gotchas

A collection of practical tips I've picked up along the way and gotchas to watch out for.

---

## Skills are not available in cloud/autonomous sessions unless committed to the repo

When you run a cloud-based autonomous session (e.g. via claude.ai tasks or remote agents), Claude starts from a fresh clone of whatever repo you point it at. Only what's committed to that repo is available.

**What IS available in cloud sessions:**

Anything in the cloned repo is available, including:
- `CLAUDE.md`
- `.claude/settings.json` hooks
- `.mcp.json` MCP servers
- `.claude/rules/`, `.claude/skills/`, `.claude/agents/`, `.claude/commands/`

**What is NOT available:**

- Your user-level `~/.claude/CLAUDE.md` — lives on your machine, not in the repo
- Your user-level `~/.claude/skills/`, `~/.claude/agents/`, `~/.claude/commands/`

**Practical implications:**

- Skills stored locally or referenced externally (like this bank) won't be available to cloud sessions unless you commit them into the target repo under `.claude/skills/`
- If you want skills available across cloud tasks, commit them into each repo's `.claude/` directory, or enable them on claude.ai itself (skills enabled on claude.ai are loaded into cloud sessions automatically)

The "skills you enable on claude.ai are loaded automatically" note is worth exploring as a way to make your skills available without committing them to every repo.
