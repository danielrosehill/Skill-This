---
name: skill-this
description: Turn the workflow just completed into a reusable skill in the right plugin. Use on "skill this", "it's a skill", "make this a skill".
---

# Skill this

The user has just watched you do something they will ask for again, and wants it
captured so they never have to redefine its scope and process. The subject is the
**workflow in this conversation**: the sequence of steps that worked, not the one-off
details of this run.

Work through the sections in order. Stop at the first exit.

## Inputs

- The workflow, taken from this conversation. If the user points at only part of it,
  capture that part.
- The deployment profile (next section).

## 0. Load the profile

Look for `profile.md` in the first of these that exists:

1. `$SKILL_THIS_DATA`
2. `$CLAUDE_USER_DATA/skill-this/`
3. `~/.claude-user-data/skill-this/`
4. `${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`

It names the user's marketplaces, clones, search tool, and how they deploy. Where it
conflicts with a default below, the profile wins. If there is no profile, carry on
with the defaults and offer the `setup` skill at the end.

## 1. Is it already a skill?

Search the user's skill catalogue, plus installed skills and local plugin clones, for
a skill that defines an orderly sequence fitting what was just done. Describe the job
in plain words, and decide by reading each candidate's description and body, never by
search rank. A skill that didn't surface during the task may still exist, so check
that the search reaches the library before trusting a miss.

**Exit** if a reasonably close match exists, meaning a superseding or more specific
skill would not have made the task go better. Name the skill, recommend creating
nothing, and stop. If the match needs a small fix to fit, offer that fix instead.

Otherwise continue.

## 2. Draft the workflow

Write down the sequence as it should run next time:

- The steps that worked, in order, with the wrong turns removed and the traps kept.
- What varies per run: these become inputs.
- What was personal to this user: names, accounts, hosts, paths. These stay out of a
  public skill.
- Which approvals the workflow needs.

## 3. Decide where it lives

**Visibility.** Default to public. Go private only when the workflow cannot work
without content that shouldn't be shared. Personal preferences alone don't force
this: ship the skill publicly and keep the preferences in user data (see below).
Infer the setting, and ask only if it is really unclear.

**Home.** If the skill fits an existing plugin, add it there. Make a new plugin only
if this skill would be a natural companion to other skills that belong together as a
group and don't yet have a home. In that case either add those companions, as agreed
with the user, or ship this skill alone in the new plugin.

**User data.** A public plugin must not hold one user's details, and must not store
them where a plugin update can overwrite them. Where a skill needs them:

- Add a `setup` (onboarding) skill that saves them outside the install directory,
  under the user-data root from the profile, in `<root>/<plugin-name>/`.
- State that path in the skills that read it, with the lookup order from step 0.
- Never write to the plugin install directory or to Claude's own config directory.

**Primitives.** Prefer the lightest thing that works:

1. A skill using a well-documented CLI or raw API requests. Don't build an MCP server
   only to define tools these already provide.
2. An existing MCP server, if one fits.
3. A custom MCP server, only when neither of those will do.

**CLIs.** If the skill needs a CLI, install it where the profile says, and do it
through the configuration that seeds that environment so it survives a rebuild, not
by hand in a running one. Any state the CLI keeps must sit on persistent storage.

**Binary files.** If the skill moves binary files to a remote tool, use the staging
route in the profile. Don't assume the tool can read local paths.

## 4. Decide on a store

Some plugins pair with a repository acting as their memory: a repair-log skill that
writes to a logs repo, for example.

- **Existing plugin:** find out whether it already has a store (repository, external
  MCP integration) and write the skill to work with it.
- **New plugin:** ask whether the user wants a companion store, public or private.
  Ask too whether users should start from a GitHub template repository. If so, add a
  skill that seeds a workspace from it with `gh repo create --template`.

Always refer to a store by its **remote URL**, never a local path, so it resolves on
every client. Hard-code the URL in a private plugin. In a public plugin that users
will point at their own store, read it from an environment variable and name that
variable in the skill and README.

## 5. Build it

Find the plugin's local clone, or clone it. Follow that plugin's conventions.

Write `skills/<name>/SKILL.md`:

- Folder name matches frontmatter `name`, in kebab-case.
- `description` is at most 140 characters and uses the words a user would say, since
  discovery ranks on it.
- Numbered, imperative steps that can run without inferring missing context.
- No absolute home paths, no literal secrets. In a public plugin, no personal
  infrastructure: search the diff for the user's names and hosts before committing.

Then follow the matching lifecycle.

**New skill in an existing plugin:**

1. Add the skill, and bump the plugin version.
2. List it in the plugin's README.
3. Commit and push.

**New plugin:**

1. Create the repository with the agreed visibility. If the profile names a plugin
   scaffolding skill, use it.
2. Write the skill, `.claude-plugin/plugin.json`, README and LICENSE. Commit and push.
3. Add the plugin to the marketplace that matches: public to public, private to
   private. Push the marketplace so the update propagates.
4. Create and push any companion store or template repository. A template must be
   marked as one: `gh repo edit <repo> --template`.

Stage named paths rather than everything in the tree.

## 6. Confirm

Check the skill itself: confirm the pushed commit contains it, and that the catalogue
returns it, using any sync step the profile gives. Install only if the user asked.

## Report

Two or three lines: the skill's id, where it lives and its visibility, and anything
the user must do. If you exited at step 1, name the existing skill and why it covers
the workflow.

## Data storage

This skill reads `profile.md` and writes nothing to the user-data root.

## Notes

This skill pushes to GitHub, and a public push is visible at once. Check that the
destination's visibility is the one you decided on before pushing.
