# skills

Agent skills by abaza738, for Claude Code and other agents that read `SKILL.md` files.

## Skills

| Skill | What it does |
|---|---|
| [design-engineer](skills/design-engineer/SKILL.md) | A design engineer's working standard for frontend and UI work: clickable mockups, interface copy, typography, tokens, shape and buttons, light and depth without heavy effects, purposeful motion, layout patterns, desktop app windows, and checking work in more than one browser engine. |

## Install

### Any agent (Claude Code, Cursor, Codex and others)

```bash
npx skills add abaza738/skills
```

### Claude Code plugin

```
/plugin marketplace add abaza738/skills
/plugin install abaza738-skills@abaza738-skills
```

This installs every skill in the repository as one plugin.

### By hand

Copy any folder under `skills/` into `~/.claude/skills/` for every project, or into `<project>/.claude/skills/` for one.

Skills load by themselves when a task matches their description. To call one directly, ask for it by name.

## Layout

```
.claude-plugin/
  marketplace.json    the marketplace, listing one plugin
  plugin.json         the plugin, which picks up every folder in skills/
skills/
  <skill-name>/
    SKILL.md          the skill
    references/       material the skill loads only when needed
```

## Adding a skill

Add a folder under `skills/` with a `SKILL.md`, add a row to the table above, and bump the version in `.claude-plugin/plugin.json`.

## License

MIT. See [LICENSE](LICENSE).
