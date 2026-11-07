# Public plugin with a private overlay

The one shape for anything that looks private. Ship a single public, generic plugin, and
keep everything specific to the user in a data folder outside it. Never a parallel private
fork, and a private companion plugin only as a last resort.

## Sort every particular

List what in the workflow is specific to this user, and give each item a home.

| Particular | Home |
|---|---|
| Values: hosts, IDs, emails, paths, account names | `profile.env` |
| Secrets | Neither: the skill names the environment variable, and the user's own secrets route fills it |
| Formats: templates, presets, voices, layouts | Overlay files, named like the built-ins |
| Procedure: "in my setup, always do X first" | `<skill-name>.md`, the skill's private notes |
| Records: logs, history, state | The plugin's workspace repository, private, by remote URL |

If the generic skill still does something useful after that, it's public.

## Private only when

- Nothing useful is left once the particulars are gone.
- The workflow's existence is itself sensitive, or it belongs to a client or employer.
- The user has a standing rule that covers it.

## The data folder

```
<user-data-root>/<plugin>/
  profile.env        KEY=value. Comments are instructions for this user
  <skill-name>.md    Private notes for one skill. They win over the skill's defaults
  templates/         Same names as the plugin's built-ins. The user's copy wins
```

Resolve the folder in this order, and stop at the first match:

1. `$<PLUGIN>_HOME`
2. `$CLAUDE_USER_DATA/<plugin>/`
3. `~/.claude-user-data/<plugin>/`, if `~/.claude-user-data` exists
4. `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/<plugin>/`, if that root exists
5. `~/.claude-user-data/<plugin>/`

Never the plugin install directory, and never Claude's own config directory. If a plugin
already has its own layout in this folder, keep it.

## What the plugin ships

1. **A `setup` skill** that writes `profile.env`, asking only for what it can't detect.
2. **A load step at the top of each skill**, which can be copied as is:

   ```markdown
   ## 1. Load your settings

   Find the data folder: `$<PLUGIN>_HOME`, `$CLAUDE_USER_DATA/<plugin>/`, then
   `~/.claude-user-data/<plugin>/`. Read `profile.env` and `<skill-name>.md` if they exist.
   They win over the defaults below. If there is no profile, use the defaults and offer
   `setup` at the end.
   ```

3. **Overlay-first lookup** for formats: the user's file by name, then the built-in. Read
   built-ins from the plugin and don't copy them, so they keep getting fixes.
4. **A `## Data storage` section** in each skill, saying what it reads and writes there.
5. **Defaults that work with no overlay**, since a stranger has none.

A new format is data, not a skill: one generic skill serves every template, and a
template's `notes:` and `triggers:` fields hold what's particular to it. A user-only skill
would need a plugin.

## Do it all in one pass

1. Write the generic skill in the public plugin, adding the load step and any new `setup`
   keys.
2. Write the user's particulars to the data folder, so the skill works for them on its
   first run. If the user-data root is a git repository, stage only this plugin's folder.
3. Search the public diff for the user's names, hosts, IDs and emails. Anything found goes
   to the overlay.
4. Ship through the normal lifecycle.
5. Report both halves: the public skill, and what was kept private and where.

## Converting an existing private plugin

This publishes existing work, so get the user's yes first.

1. Sort its particulars as above, and move them into the data folder.
2. Rewrite its skills generically, and add `setup` and the load steps.
3. If the old history holds anything private, don't flip the repository's visibility, and
   don't rely on a squash: old commits stay reachable by SHA once public. Keep a private
   bundle of the old history, create a new repository from one clean commit, confirm an
   old SHA returns 404, and only then make it public.
4. Move the marketplace entry from the private marketplace to the public one in the same
   change. A plugin is listed in one marketplace only.
5. Confirm the catalogue shows the plugin under its new marketplace, at the new commit.
