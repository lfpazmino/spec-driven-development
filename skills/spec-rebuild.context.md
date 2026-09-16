# Spec Rebuild Context

A cleared or brand-new session has no memory of the project but full access to the repo. This skill
turns that into working context: it makes sure `CLAUDE.md` exists so future sessions load the
project automatically, then runs a disciplined read-only sweep and reports back before anything is
touched.

Use it after `/clear`, when picking up an unfamiliar spec-driven repo, or when asked to "get up to
speed" or "rebuild context".

**Do not** use it as a general repo explainer on a project that has no requirements contract - say so
and stop instead (Step 0).

## The one hard rule

**Exactly one write is permitted: `CLAUDE.md`, and only if it does not already exist.** Everything
else in this skill is read-only. No edits, no commits, no branches, no "while I was in there" fixes.
The session ends with a report and a question, not with changes.

---

## Step 0 - Find the contract

Look in the working directory for:

| File | What it is |
| --- | --- |
| `README.md` | The requirements contract. Requirements carry IDs (`R-01`, `NFR-01`) and provenance tags |
| `TODO.md` | What is next - `## Now` and `## Next`. The ordering authority |
| `CLAUDE.md` | Persistent project instructions, loaded every session |
| `prototype/README.md` | What the prototype proves and what is throwaway |
| `spec.md`, `specs/` | Present once `spec-dev` has run |
| `specs/YYYY-MM-DD-phase-name/` | Per-phase specs: requirements, plan, validation, architecture |
| `architecture/model.c4` | The single living LikeC4 model - the structural source of truth |

If there is no `README.md` carrying requirement IDs and no `spec.md`, this is not a spec-driven
project. Say that plainly, name what you did find, and stop. Do not improvise a substitute
orientation.

## Step 1 - CLAUDE.md

**If `CLAUDE.md` already exists:** read it. Do not overwrite it, do not "improve" it. Note what it
covers, and at report time mention anything important it is missing - as a suggestion, not an edit.

**If it does not exist:** read `README.md`, `TODO.md` and `prototype/README.md` first, then write one.
Keep it under 200 lines; longer files reduce adherence. Cover only what a future session needs in
*every* conversation:

- **What this is** - two or three sentences, plus where the deployed thing lives if there is one.
- **Method** - which skills drive the project (`spec-dev` iterates through roadmap phases,
  `spec-phase` targets a specific one), and that requirement IDs are stable and must never be
  renumbered.
- **Provenance discipline** - if the README tags requirements `[stated]` / `[agreed]` /
  `[inferred]`, state that `[inferred]` means unconfirmed and must not be silently promoted. Give
  the current inferred count.
- **Non-negotiable constraints** - the technical decisions that are settled, and which ones are
  marked *Revisit* and need a decision rather than a quiet change.
- **Out of scope** - what was explicitly ruled out, so it does not get reintroduced as an
  improvement.
- **Working style** - read before proposing; do not refactor a `prototype/` directory; never put a
  `.c4` file inside `specs/`; surface contradictions rather than resolving them in favour of the
  code.

Start the file with `@README.md` and `@TODO.md` imports so both load at session start.

Do not invent constraints. Everything in `CLAUDE.md` must be traceable to something you read.

**Then say this out loud in the report:** a newly written `CLAUDE.md` does not load into the current
session - it is read at session start. You have read it directly, and it will load automatically
from the next session onward.

## Step 2 - Orientation sweep (read-only)

1. **The contract.** `README.md` and `TODO.md`. Record every requirement still tagged `[inferred]`
   and how many there are. These are unconfirmed assumptions, not requirements. Note what `TODO.md`
   has under `## Now`.
2. **The prototype.** `prototype/README.md` - specifically what it *proves* versus what is listed as
   throwaway. Do not read the prototype source yet.
3. **The phase history.** List `specs/YYYY-MM-DD-*/` directories in order and read the most recent
   one's `requirements.md`. That tells you where the project actually got to, which is often not
   where `roadmap.md` claims.
4. **The model.** `architecture/model.c4` - the components that exist and which phase tags are in
   use. Structure lives here, never in a spec folder.
5. **Skills.** Report which relevant skills are available in this session, from the skill listing
   already in context - in particular `spec-dev` and `spec-phase`. You cannot run `/skills`
   yourself; it is a user-typed command. Tell the user to run `/skills` to confirm, and if the
   skills the project depends on are absent, **stop and say so** rather than improvising an
   equivalent process. On the Claude Code CLI, account skills need a one-time
   `CLAUDE_CODE_SYNC_SKILLS=1 claude -p "list skills"` to appear.
6. **Shape, not contents.** Identify the largest data or content file the project depends on. Read
   enough to learn its *structure* - the shape of each entity and how they nest - and stop. Never
   read a large data file end to end; it buys almost nothing and spends the context the user just
   cleared.

## Step 3 - Report, in this order

Keep it short. This is an orientation, not an essay.

1. **What this project is** - three sentences. No more.
2. **The Now item** from `TODO.md`, and whether the code as it stands supports starting it. Say
   plainly if it does not.
3. **Contradictions** between the docs and the actual code - a requirement the code does not meet, a
   documented constraint the code violates, a component in `architecture/model.c4` that no longer
   exists in the code (or the reverse), a roadmap phase marked complete whose spec folder is missing.
   This is the highest-value part of the sweep; it is the only way drift gets caught. If there are
   none, say so explicitly rather than staying silent.
4. **What you need from the user** before writing anything - decisions, confirmations, access.

End by stating that you are read-only until they confirm.

## Failure modes to avoid

- **Overwriting an existing `CLAUDE.md`.** It may contain team decisions you cannot see the reasons
  for.
- **Treating `[inferred]` as settled.** It is the single most consequential distinction in a
  spec-driven repo.
- **Trusting `roadmap.md` over the spec folders.** The roadmap is intent; the dated folders are what
  happened. When they disagree, the folders win and the roadmap is drifting.
- **Reading a large content file in full** because it seemed thorough. It defeats the purpose of a
  cleared session.
- **Working around a missing skill.** A plausible substitute process is worse than stopping, because
  it looks correct.
- **Starting work at the end of the report.** The report is the deliverable. Wait.