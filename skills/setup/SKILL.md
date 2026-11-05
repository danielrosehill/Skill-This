---
name: setup
description: Save your gateway URLs, marketplaces and clones so skill-this builds skills for your deployment. Use on "set up skill-this".
---

# Set up skill-this

Creates or updates the short profile that `skill-this` and `improve-skill` read on
every run. Everything is optional: an empty value means the default applies.

## Steps

1. Choose the data directory: `$SKILL_THIS_DATA` if set, otherwise
   `$CLAUDE_USER_DATA/skill-this/`, otherwise `~/.claude-user-data/skill-this/` if
   `~/.claude-user-data` exists, otherwise
   `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`.
2. If `profile.env` exists there, read it and ask only what is missing or changed.
3. Fill in what you can first: the MCP entry in Claude's config for the gateway URL,
   `claude plugin marketplace list`, and the `git` remotes of any clones. Ask the user
   for the rest in one message.
4. Write `profile.env` using the template below, and show the user its path.

Never put a token in this file. Name the variable that holds it instead.

## Template

```bash
# Canopy gateway: the MCP URL on each route. Tried in this order.
CANOPY_URL_TAILSCALE=
CANOPY_URL_LAN=
CANOPY_URL_PUBLIC=

# Marketplaces, as owner/repo
SKILL_THIS_PUBLIC_MARKETPLACE=
SKILL_THIS_PRIVATE_MARKETPLACE=

# Local folder holding plugin clones
SKILL_THIS_PLUGIN_ROOT=

# Where plugins keep user data, in <root>/<plugin-name>/
SKILL_THIS_USER_DATA_ROOT=

# Anything else particular to this deployment goes in comments, as instructions.
```

## Data storage

Writes `profile.env` to the data directory above and nowhere else. The file lives
outside the plugin install directory, so plugin updates leave it alone.
