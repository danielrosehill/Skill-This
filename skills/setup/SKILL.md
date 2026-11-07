---
name: setup
description: Save your gateway, clones and marketplace map for skill-this. Use on "set up skill-this" or "refresh marketplaces".
---

# Set up skill-this

Creates or updates the short profile and marketplace map that `skill-this` and
`improve-skill` read on every run. Everything is optional: an empty value means the
default applies.

## Steps

1. Choose the data directory: `$SKILL_THIS_DATA` if set, otherwise
   `$CLAUDE_USER_DATA/skill-this/`, otherwise `~/.claude-user-data/skill-this/` if
   `~/.claude-user-data` exists, otherwise
   `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`.
2. If `profile.env` exists there, read it and ask only what is missing or changed.
3. Fill in what you can first: the MCP entry in Claude's config for the gateway URL,
   `claude plugin marketplace list`, and the `git` remotes of any clones. Ask the user
   for the rest in one message.
4. Write `profile.env` using the template below.
5. Build `marketplaces.md` as described below, and show the user both paths.

Never put a token in this file. Name the variable that holds it instead.

## Template

```bash
# Canopy gateway: the MCP URL on each route. Tried in this order.
CANOPY_URL_TAILSCALE=
CANOPY_URL_LAN=
CANOPY_URL_PUBLIC=

# Local folder holding plugin clones
SKILL_THIS_PLUGIN_ROOT=

# Where plugins keep user data, in <root>/<plugin-name>/
SKILL_THIS_USER_DATA_ROOT=

# Anything else particular to this deployment goes in comments, as instructions.
```

## The marketplace map

To refresh, on "refresh marketplaces" or when `skill-this` asks, run this part alone. Keep
existing purposes and rules, and change only what differs.

1. List the marketplaces, using the first source that works: `canopy marketplaces`, the
   gateway's `marketplaces.json`, or `claude plugin marketplace list` if there's no gateway.
2. For each, get visibility and default branch:
   `gh api repos/<owner/repo> --jq '[.visibility,.default_branch]|@tsv'`.
3. Find its local clone, under the marketplace folder if the user has one.
4. Ask the user for the purpose of any new marketplace, and for what else belongs in its rules.
5. Remove rows for marketplaces that have gone, and set `Checked` to today.

```markdown
# Marketplaces

Checked: YYYY-MM-DD. Source: the gateway's marketplace list, which wins over this file.

| Name | Repo | Branch | Visibility | Clone | What goes here |
|---|---|---|---|---|---|
| my-plugins | owner/repo | main | public | ~/path/to/clone | General plugins |

## Rules

### my-plugins

- Anything a push to this marketplace needs: required fields, generated files, changelog.
```

## Data storage

Writes `profile.env` and `marketplaces.md` to the data directory above and nowhere else. The files live
outside the plugin install directory, so plugin updates leave them alone.
