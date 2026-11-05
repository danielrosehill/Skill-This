---
name: setup
description: Save your marketplaces, plugin clones and deployment preferences so skill-this builds skills your way. Use on "set up skill-this".
---

# Set up skill-this

Creates or updates the user's deployment profile, which `skill-this` reads on every
run. Everything here is optional: a blank field means the skill's default applies.

## Steps

1. Choose the data directory: `$SKILL_THIS_DATA` if set, otherwise
   `$CLAUDE_USER_DATA/skill-this/`, otherwise `~/.claude-user-data/skill-this/` if
   `~/.claude-user-data` exists, otherwise
   `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`.
2. If `profile.md` exists there, read it and ask only what is missing or changed.
3. Fill in what you can from the environment first: `claude plugin marketplace list`,
   and the `git` remotes of any clones. Ask the user for the rest in one message.
4. Write `profile.md` using the template below, and show the user its path.

## Template

```markdown
# skill-this profile

## Marketplaces
- Public: <owner/repo>, local clone <path>
- Private: <owner/repo>, local clone <path>

## Plugins
- Local clone root: <path>
- Scaffolding skill: <skill id, or none>

## Discovery
- Catalogue search: <tool or command>
- Sync after push: <tool or command, or automatic>

## Deployment
- How plugins reach the agent: <installed locally, or served from a gateway>
- Where CLIs get installed: <environment, and the file that seeds it>
- Persistent storage for CLI state: <mount or path>
- Binary file transport: <route, and how to clean up>

## User data
- User-data root for plugins: <path>
```

## Data storage

Writes `profile.md` to the data directory above and nowhere else. The file lives
outside the plugin install directory, so plugin updates leave it alone.
