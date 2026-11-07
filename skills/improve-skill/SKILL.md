---
name: improve-skill
description: Find an existing skill, even from a vague memory, and update it with what was just learned. Use on "improve skill", "update that skill".
---

# Improve a skill

Also use on "update the skill", "fix that skill", and "we should put this in the skill".

The user knows a skill exists and wants it updated, and may not remember its name.
Something in this workflow is worth encoding: a kink that was overcome, or a better way
of doing it. This is the counterpart to `skill-this`, which fills a gap. If it turns
out no such skill exists, say so and offer `skill-this`.

Work through these steps in order.

## 1. Load the profile

Read `profile.env` as described in `skill-this`, in the first of these that exists:
`$SKILL_THIS_DATA`, `$CLAUDE_USER_DATA/skill-this/`, `~/.claude-user-data/skill-this/`,
`${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins/skill-this/`. Environment
variables of the same name override the file. Read `marketplaces.md` and `improve-skill.md`
there too, if they exist.

## 2. Find the skill

- **Named:** go straight to it.
- **Not named:** start with skills used or loaded in this conversation, since the user
  usually means one of those. Otherwise search the catalogue in plain words for the job
  it does, and also try the words the user used. Read each candidate's description, not
  its search rank.
- **One clear match:** carry on, and name it in the report.
- **Several plausible matches:** ask, listing each with its one-line description.

## 3. Work out what to encode

Take it from what the user said, and from this conversation:

- **A kink:** what went wrong, what caused it, and what fixed it.
- **A better way:** the step it replaces, and why it's better.

Decide where it belongs before editing. If it's true only for this user, it goes in the
plugin's data folder as a skill note or profile entry, not the skill: see
`skill:skill-this/skill-this/references/public-with-overlay.md`.

Keep to what actually happened, with the real commands and tools. If the user stated
the change, encode that. If you can't tell what they want encoded, propose it in one
or two lines and ask.

## 4. Update it

1. Find the plugin's local clone, under `SKILL_THIS_PLUGIN_ROOT` if set, or clone it.
   Pull first, and read the whole current skill.
2. Edit in place. Change the step that was wrong rather than adding a caveat beside it,
   and put a trap in the step where it bites. Leave everything else as it is.
3. Update `description` if the skill now covers different ground. Keep it to 140
   characters at most.
4. If the plugin is public, keep it generic: anything specific to this user goes in the
   plugin's user data, not the skill. Search the diff for the user's names and hosts.
5. Bump the plugin version, and update the plugin's README if it describes the changed
   behaviour.
6. Commit with a message saying what was learned, and push. Stage named paths only.

## 5. Propagate and report

The `marketplace` field of `catalogue__read plugin:<name>` says where it's listed, and
`marketplaces.md` gives that marketplace's clone, branch and rules. If the entry records a
version, update it and push the marketplace. If the plugin is installed locally, run
`claude plugin update <plugin>@<marketplace>`. Then verify as in step 7 of `skill-this`:
`catalogue__sync plugin=<name>`, and `head` from `catalogue__read` must equal your commit.

Report in two lines: which skill you changed, and what it now does differently.

## Data storage

This skill reads `profile.env`, `marketplaces.md` and `improve-skill.md` from the data
folder. It writes to `marketplaces.md`, and to a plugin's data folder when a change is
specific to this user.
