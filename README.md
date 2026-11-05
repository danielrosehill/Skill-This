# Skill-This

Turn the workflow you just finished into a reusable skill, so you never have to
redefine its scope and process.

Say "this is a skill" at the end of a task. The agent checks your catalogue for an existing
match, and if there isn't one, drafts the skill from the conversation, picks the right
plugin (or a new one), and publishes it.

It runs after a task, on work you've already done. To decide *before* a task whether
it should be a skill, see [To-Skill-Or-Not-To-Skill](https://github.com/danielrosehill/To-Skill-Or-Not-To-Skill).

## Skills

- `skill-this`: capture the workflow just completed as a skill
- `setup`: save your marketplaces, clones and deployment preferences

## What it decides

- **Already covered?** A reasonably close existing skill means nothing gets created.
- **Public or private?** Public by default. Personal preferences go in user data, not
  in the plugin.
- **Which plugin?** An existing one if it fits. A new one only for a natural group.
- **Which primitive?** A skill on a CLI or API first, then an existing MCP server,
  then a custom one.
- **Which store?** Companion repositories and GitHub templates, always referenced by
  remote URL so they resolve on every client.

## Your data

`setup` writes `profile.md` to the first of these that applies, outside the plugin
install directory so updates leave it alone:

1. `$SKILL_THIS_DATA`
2. `$CLAUDE_USER_DATA/skill-this/`
3. `~/.claude-user-data/skill-this/`
4. `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`

## Installation

```bash
claude plugins marketplace add danielrosehill/Claude-Code-Plugins
claude plugins install skill-this@danielrosehill
```

## License

MIT
