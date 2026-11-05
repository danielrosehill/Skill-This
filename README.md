# Skill-This

Turn the workflow you just finished into a reusable skill, so you never have to
redefine its scope and process.

Say "this is a skill" at the end of a task. The agent checks your catalogue for an existing
match, and if there isn't one, drafts the skill from the conversation, picks the right
plugin (or a new one), and publishes it.

It runs after a task, on work you've already done. To decide *before* a task whether
it should be a skill, see [To-Skill-Or-Not-To-Skill](https://github.com/danielrosehill/To-Skill-Or-Not-To-Skill).

## Skills

- `skill-this`: there's a gap, so capture the workflow just completed as a new skill
- `improve-skill`: there's already a skill, even if you can't remember its name, so
  find it, update it with what you just learned, and push it
- `setup`: save your gateway URLs, marketplaces and clones

## What it decides

- **Already covered?** A reasonably close existing skill means nothing gets created.
- **Public or private?** Public by default. Personal preferences go in user data, not
  in the plugin.
- **Which plugin?** An existing one if it fits. A new one only for a natural group.
- **Which primitive?** A skill on a CLI or API first, then an existing MCP server,
  then a custom one.
- **Which store?** Companion repositories and GitHub templates, always referenced by
  remote URL so they resolve on every client.

## Built for Canopy

Skills it writes target [Canopy](https://github.com/danielrosehill/Canopy), the
open-source AI gateway, with your computer as a thin client:

- Skills are found through Canopy's catalogue, not installed on each client.
- CLIs are seeded in Canopy's Alpine `cli` container (`cli/Dockerfile`), run with
  `canopy run`, and keep their state on its persistent mount.
- Binary files travel through Canopy's S3 staging bucket with `canopy stage`, which
  expires them automatically.

Without Canopy, record your own setup with `setup`.

## Your data

`setup` writes a short `profile.env`, outside the plugin install directory so updates
leave it alone. It goes in the first of these that applies:

1. `$SKILL_THIS_DATA`
2. `$CLAUDE_USER_DATA/skill-this/`
3. `~/.claude-user-data/skill-this/`
4. `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`

Environment variables of the same name override it. The main ones are
`CANOPY_URL_TAILSCALE`, `CANOPY_URL_LAN` and `CANOPY_URL_PUBLIC`.

## Installation

```bash
claude plugins marketplace add danielrosehill/Claude-Code-Plugins
claude plugins install skill-this@danielrosehill
```

## License

MIT
