---
name: skill-this
description: Turn the workflow just completed into a reusable skill in the right plugin. Use on "this is a skill", "it's a skill", "skill this".
---

# Skill this

Also use on "extract this as a skill", "make a skill from what we just did", and "this
should be a skill".

Pause the task. The process just followed is a repetitive workflow the user will ask
for often, and they don't want to redefine its scope and process every time. Capture
it as a skill.

The subject is the **workflow in this conversation**. If it holds several distinct
procedures, ask which to capture, or capture each as its own skill.

Work through these steps in order.

## 1. Load the profile

Look for `profile.md` in the first of these that exists:

1. `$SKILL_THIS_DATA`
2. `$CLAUDE_USER_DATA/skill-this/`
3. `~/.claude-user-data/skill-this/`
4. `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`

It names the user's marketplaces, local clones, catalogue tools and deployment. Where
it differs from a default below, the profile wins. If there is no profile, use the
defaults and offer the `setup` skill at the end.

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
- **No match:** continue.

## 3. Where can this live?

Skills are bundled in plugins. The user keeps a public and a private marketplace, and
both are wrappers that package skills for delivery.

### Public or private

Public versus private is the boundary between workflows that can be open sourced and
may benefit others, and those not appropriate for sharing. **Bias strongly towards
open source.** Infer the setting, and confirm with the user only if in doubt.

A need for the user's own preferences or data is not a reason to go private. Keep the
plugin public and use a user-data store:

- The plugin ships an onboarding skill that saves configuration, including environment
  variables, to a part of the filesystem Claude does not manage.
- Skills and the plugin's docs state clearly where that data lives, so user data never
  conflicts with delivering the plugin or with its updates. For example, a
  `network-security` plugin stores user notes in `<user-data-root>/network-security/`.
- Never store it in the plugin install directory or in Claude's own config directory.

### Existing plugin or new plugin

If the skill fits within an existing plugin, add it to that plugin.

The exception: if, looking at the existing plugins and skills, the proposed skill
would be a natural complement to additional new skills that could be wrapped together
as a gathering of specific skills, create a new plugin. Then either add those
complementary skills, as agreed with the user, or add this as the only skill in the
new plugin.

### Deployment defaults

Plugins are optimised for this deployment, however they are shared. The profile gives
the specifics.

- **Delivery:** plugins are deployed to an AI gateway server that holds the plugins
  and an MCP gateway.
- **Client:** the user's laptop or desktop is a thin client. Claude keeps a user-level
  connection to the gateway and finds skills through its discovery mechanism.
- **Skills over MCP servers:** skills and plugins are the preferred way to ship
  primitives. If a well-documented CLI or raw API requests can do what an MCP server
  would do, don't create one just to define tools. Where an MCP server is required,
  prefer an existing one that fits the skill surface. Create a custom one only if
  that can't be avoided.
- **CLIs:** a CLI can go on the host, but preferably goes in the Alpine Linux
  container that is part of the gateway deployment. If a CLI is required, first update
  the Alpine configuration to seed its installation, and make sure the container keeps
  a persistent volume mount so its configuration survives host reboots.
- **Binary files:** where a plugin needs to transport binary files (transcription
  tools, for example), include a skill that uses the gateway's S3 bucket as staging
  storage. The bucket has a lifecycle policy that deletes files automatically, so
  don't configure cleanup.

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

## 6. Lifecycle

### Skill in an existing plugin

1. Identify the plugin that is the natural home for the skill.
2. Find where it is cloned on the local filesystem, or clone it if it is not.
3. Add the new skill or skills, and bump the plugin version.
4. Push the repository.

### Skill in a new plugin

1. Create the new plugin with the desired privacy, confirming with the user in cases
   of ambiguity. If the profile names a scaffolding skill, use it.
2. Author the new skill within it, and push.
3. Add the plugin to the corresponding marketplace: private plugins to the private
   marketplace, public plugins to the public marketplace.
4. Push the marketplace to propagate the update.
5. If the user wants a complementary repository for storing information, create and
   push that too, along with any template repository.

Stage named paths rather than everything in the tree. Check the destination's
visibility before pushing, since a public push is visible at once.

## 7. Confirm and report

Confirm the pushed commit contains the skill, and that the catalogue returns it, using
any sync step the profile gives. Install only if the user asked.

Report in two or three lines: the skill's id, where it lives and its visibility, and
anything the user must do.

## Data storage

This skill reads `profile.md` and writes nothing to the user-data root.
