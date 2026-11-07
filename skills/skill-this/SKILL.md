---
name: skill-this
description: Turn the workflow just completed into a reusable skill in the right plugin. Use on "this is a skill", "it's a skill", "skill this".
---

# Skill this

Also use on "extract this as a skill", "make a skill from what we just did", "this
should be a skill", and "could this be public".

Pause the task. The process just followed is a repetitive workflow the user will ask
for often, and they don't want to redefine its scope and process every time. Capture
it as a skill.

The subject is the **workflow in this conversation**. If it holds several distinct
procedures, ask which to capture, or capture each as its own skill.

Work through these steps in order.

## 1. Load the profile

Find the data folder, the first of these that exists:

1. `$SKILL_THIS_DATA`
2. `$CLAUDE_USER_DATA/skill-this/`
3. `~/.claude-user-data/skill-this/`
4. `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`

Read what's there:

- `profile.env`: a short `KEY=value` file with the particulars of this deployment: the
  gateway URLs and clones. Environment variables of the same name override it. Comments
  in the file are instructions for this user.
- `marketplaces.md`: the map of where plugins get pushed.
- `skill-this.md`: this user's private rules for this skill.

The user's comments and rules win over a default below.

To reach the gateway directly, try `CANOPY_URL_TAILSCALE`, then `CANOPY_URL_LAN`, then
`CANOPY_URL_PUBLIC`, and use the first that answers. If there is no profile, use the
defaults and offer the `setup` skill at the end.

### The marketplace map

With a gateway, Claude's own marketplace list is not the real one: the gateway indexes
marketplaces this client never added. `marketplaces.md` is this plugin's memory of them,
holding each one's repository, branch, visibility, clone, purpose and rules.

- Run the refresh in `setup` if the file is missing, its `Checked` date is over 30 days
  old, or the job needs a marketplace it doesn't list. The gateway's list wins over the file.
- For a plugin that already exists, the `marketplace` field of
  `catalogue__read plugin:<name>` is the answer. It wins over the map.
- When a push teaches something about a marketplace, add a line to its rules.

## 2. Is it already a skill?

Check the skills catalogue to verify that the workflow isn't already covered by an
existing skill that didn't surface during the task. Also check installed skills and
local plugin clones.

"Matching" means the skill defines an orderly sequence that fits well onto what was
just achieved. Describe the job in plain words, and judge candidates by reading their
descriptions and bodies, never by search rank. Make sure the search actually reaches
the library before trusting a miss.

- **Close match found:** if a superseding or more specific skill would not have made
  the task more effective, report the skill to the user, recommend not creating a new
  one, and stop.
  If this run improved on that skill, offer `improve-skill` instead.
- **No match:** continue to the reshape check.

### Reshape before creating

No match is not yet a reason to create. Most new workflows are an existing skill in a
different shape, so look at the nearest few candidates and try each of these, cheapest
first:

| Move | When | Result |
|---|---|---|
| **Note** | Only this user's preference differs | A line in that skill's private notes, and no skill change |
| **Data** | Only the format or values differ | A template or profile entry, and one skill serves both |
| **Widen** | Same job on a new target | A parameter or mode on the existing skill |
| **Absorb** | A variant of an existing procedure | A branch or section in that skill |
| **Generalise** | This and a specific skill are cases of one job | One generic skill, with the specifics moved to data |
| **Merge** | Two skills already overlap, and this joins them | One skill. Delete the others |
| **Split** | An existing skill does two jobs, and this is one | Two clean skills, one of them reused here |

The test: the reshaped skill must still do one job, and say so within the description
limit. If it can't, the reshape is wrong and a new skill is right.

Name the move and what it replaces in one or two lines. Note, Data, Widen and Absorb go
ahead through `improve-skill`. Generalise, Merge and Split change what others may rely on,
so confirm first if the plugin is public or the choice is close. A replaced skill is
deleted, not left beside its successor.

If no move fits, continue, and say in the report which you ruled out.

## 3. Where can this live?

Skills are bundled in plugins, and marketplaces are wrappers that package them for
delivery. The user may keep several. Each one's purpose is in `marketplaces.md`: pick by
purpose, not just by visibility. A plugin is listed in exactly one marketplace.

### Public or private

Public versus private is the boundary between workflows that can be open sourced and
may benefit others, and those not appropriate for sharing. **Bias strongly towards
open source.** Infer the setting, and confirm with the user only if in doubt.

Don't ask whether the workflow is private. Ask **which parts** are. A need for the user's
own preferences or data is not a reason to go private: the answer is a public plugin with
a private overlay, the one shape used everywhere. Read `references/public-with-overlay.md`
in this skill's folder and follow it. From the catalogue, read
`skill:skill-this/skill-this/references/public-with-overlay.md`.

**Be proactive.** Whenever the workflow looks private, or its natural home is a private
plugin, say so and propose the split in a line: "this looks private, but only X and Y
are, so: public skill Z, with X and Y in your overlay". Then do all of it in one pass, as
the reference describes. Go private only on its "Private only when" list, and say which
item applied.

Where a skill lands in a private plugin that could be public as a whole, offer the
conversion as well. Don't do it unasked.

### Existing plugin or new plugin

If the skill fits within an existing plugin, add it to that plugin.

The exception: if, looking at the existing plugins and skills, the proposed skill
would be a natural complement to additional new skills that could be wrapped together
as a gathering of specific skills, create a new plugin. Then either add those
complementary skills, as agreed with the user, or add this as the only skill in the
new plugin.

### Deployment: built for Canopy

Plugins are optimised for this deployment, however they are shared. The design is
[Canopy](https://github.com/danielrosehill/Canopy), the open-source AI gateway: it
holds the plugins and an MCP gateway, and agents find skills through it. Design every
skill for this model. The profile gives the instance details.

- **Client:** the user's laptop or desktop is a thin client. Claude keeps a user-level
  connection to Canopy and discovers skills through its catalogue, so a skill must not
  assume anything is installed on the client.
- **Skills over MCP servers:** skills and plugins are the preferred way to ship
  primitives. If a well-documented CLI or raw API requests can do what an MCP server
  would do, don't create one just to define tools. Where an MCP server is required,
  prefer an existing one that fits the skill surface, registered on Canopy. Create a
  custom one only if that can't be avoided.
- **CLIs:** a CLI can go on the host, but preferably goes in Canopy's Alpine `cli`
  container. If a CLI is required, first seed its installation by adding it to
  `cli/Dockerfile` in the Canopy repo, and have the skill call it through
  `canopy run <command>`. The container's home is the persistent `agent-data/cli`
  mount, so logins and configuration survive host reboots. Keep any CLI state there.
- **Binary files:** tools on the gateway can't read paths on the thin client. Where a
  plugin needs to transport binary files (transcription tools, for example), include
  a skill that stages them in Canopy's S3 bucket with `canopy stage <file>` and passes
  the presigned URL on. Use `--audience stack` if a service on the gateway fetches the
  file, and the default `remote` audience for anything outside it. The `transient`
  bucket has a lifecycle policy that deletes files after a day, so don't configure
  cleanup.

## 4. Workspace repositories

A frequent pattern: a public or private GitHub repository acts as the workspace and
memory store for the actions a plugin performs. For example, a Home Assistant
operations plugin with a private repository for logs of actions taken, where the
plugin's repair-log skill writes each repair to that repository. The two are a
complementary system.

- **New plugin:** ask the user whether they want this pattern.
- **New skill in an existing plugin:** investigate whether the plugin has a
  complementary store, whether a repository or an external MCP integration, and edit
  the skill to work with that system as effectively as possible.

Reference the repository by its **remote URL, never a local path**, so it resolves
across clients. Hard-code the URL in a private plugin. In a public plugin the user
might customise with their own information, provide it as an environment variable.

Some plugins also rely on a **GitHub template repository**, to jumpstart a workspace.
Create it as a template repository, public or private (`gh repo edit <repo> --template`),
and reference it in a skill that seeds the workspace, using `gh repo create --template`.

## 5. Write the skill

Write `skills/<name>/SKILL.md` from what actually happened:

- **Stay faithful to the worked example.** Don't add steps that weren't taken. Drop
  the wrong turns, and keep what caused them as gotchas.
- **Keep the specifics.** Name the real commands, tools and skills. "Run the relevant
  command" is useless.
- **Take trigger phrases from the conversation**, in the words the user actually used.
- **Decisions the user made** become inputs or "ask before X" rules, and things they
  corrected become hard rules.
- **Frontmatter:** `name` in kebab-case, matching the folder. `description` at most
  140 characters, using words a user would say, because discovery ranks on it.
- **No absolute home paths and no literal secrets.** In a public plugin, no personal
  infrastructure either: search the diff for the user's names and hosts before
  committing.
- **Say where scripts live.** A skill read from the catalogue has no
  `${CLAUDE_PLUGIN_ROOT}`, so if it ships scripts, state the repository to clone when
  the variable is unset.
- **Public skills get the load step** from `references/public-with-overlay.md`.

## 6. Lifecycle

### Skill in an existing plugin

1. Identify the plugin that is the natural home for the skill.
2. Find where it is cloned on the local filesystem, or clone it if it is not.
3. Add the new skill or skills, and bump the plugin version.
4. Push the repository.
5. If the marketplace entry records a version, update it and push the marketplace.

### Skill in a new plugin

1. Check the name is free with `catalogue__read plugin:<name>`. Plugin names are global
   across marketplaces, and on a clash the later one wins.
2. Create the new plugin with the desired privacy, confirming with the user in cases
   of ambiguity. If the user's rules name a scaffolding skill, use it. A private
   repository must be readable by the gateway's token, or it will never be indexed.
3. Author the new skill within it, and push.
4. Add the plugin to the marketplace `marketplaces.md` gives for its purpose, following
   that marketplace's rules. Without an entry the gateway never sees the plugin.
5. Push the marketplace to the branch the map gives. Default branches differ.
6. If the user wants a complementary repository for storing information, create and
   push that too, along with any template repository.

Stage named paths rather than everything in the tree. Check the destination's
visibility before pushing, since a public push is visible at once.

## 7. Confirm and report

Check the index itself, not the sync's report:

1. Run `catalogue__sync plugin=<name>`, with `force=true` if the commit didn't move. It
   returns at once, and the `last` block it prints is the previous run.
2. `catalogue__read plugin:<name>`: `head` must equal the commit you pushed, and
   `contains` must list the skill. Allow a minute and retry before calling it a failure.
3. `catalogue__search` a phrase from the new description. New records may have no
   embeddings yet, so use its exact words.
4. `catalogue__health` should say `ok`.

Install only if the user asked. If the plugin is already installed here, update it.

Report in two or three lines: the skill's id, where it lives and its visibility, what
was kept in the overlay, any reshape ruled out, and anything the user must do.

## Data storage

This skill reads `profile.env`, `marketplaces.md` and `skill-this.md` from the data
folder. It writes only to `marketplaces.md`, and to the data folder of the plugin it is
shipping when it saves that plugin's overlay.
