# Spec Phase

Specs and implements **one specific roadmap phase** - the one the user names.

This is the deliberate-choice half of the pair:

| Skill | Picks the phase |
| --- | --- |
| `spec-dev` | Automatically - the next unchecked phase, iterating through the roadmap |
| `spec-phase` | The user names it. Never auto-selected |

`spec-dev` also owns the full project lifecycle: project spec, constitution, MVP cut-off, brownfield onboarding. If the project has no `specs/mission.md` yet, stop and use `spec-dev` instead - there is no constitution to spec against.

## Hard rules

1. **Never auto-select the phase.** If the user did not name one, ask (Step 1). Taking the next phase silently is `spec-dev`'s behaviour, and doing it here defeats the reason this skill exists.
2. **Never write a spec file before an `AskUserQuestion` round.** One call, questions grouped on the files being written.
3. **Never run git.** Print `git checkout -b ...`, `git commit`, `git merge`, `git branch -d` as commands for the user to run. Read-only git (`status`, `log`, `diff`) is fine and encouraged.
4. **Stop after writing the spec.** Sign-off comes before any implementation.
5. **Verify library facts through context7** (`resolve-library-id` -> `get-library-docs`) before pinning a version, API or config. Never from memory. This includes LikeC4 predicate syntax.
6. **One model, and it lives in `architecture/`.** Never write a `.c4` file inside `specs/` - LikeC4 composes every `.c4` in the workspace into one model, so a second one collides or fragments; and a dated spec folder is a historical record, while the model is alive. The phase gets a *view*, not a copy.
7. **Match the file shapes in Step 5 exactly.** Heading depth and title format are house style, not decoration - a file that is off gets hand-fixed every time.

## File conventions

Every file this skill writes follows these:

- **One `#` per file: `# {Phase-Name} - {Type}`** - `# user-auth - Requirements`, `# user-auth - Plan`, `# user-auth - Validation`, `# user-auth - Architecture`. Phase name first, hyphen, type. Never open with `# {Project}`.
- **Sections are `##`, sub-sections `###`.** Never deeper.
- **Hyphen, not em dash**, throughout titles and headings.
- **Blank line after every heading.**
- **Title Case for headings.**
- **No HTML comment header.** Reference templates carry one; generated files do not.

---

## Step 1 - Resolve the phase

Read `specs/roadmap.md` first. Then:

**If the user named a phase** - by number ("phase 2", "2.3"), by title, or by what it delivers - resolve it against the roadmap and say which phase you matched, in full, before going further. A wrong match is expensive four files later.

**If the named phase does not exist in the roadmap**, stop. List the phases that do exist with their status and ask which was meant. Do not invent a phase, and do not quietly pick the closest one.

**If the user named nothing**, list the roadmap phases with their status - complete, in progress, not started - and ask which one to work on. Suggest the next unchecked phase as the likely answer, and note that `spec-dev` takes it automatically if that is all they want. Do not proceed on the suggestion alone.

### Working out of order

The point of this skill is that the user may deliberately jump the queue. Allow it - but never silently:

- Say which earlier unchecked phases are being skipped.
- Name any dependency the chosen phase has on work that has not happened yet, and what will have to be stubbed or deferred because of it.
- If `TODO.md`'s **Now** section points somewhere else, say so. Here the user's explicit choice wins over `TODO.md`; report the divergence rather than resolving it silently, and ask whether `TODO.md` should be updated.

That report is part of the deliverable, not a caveat. A phase specced out of order without its dependencies named is how a roadmap quietly stops being true.

## Step 2 - Load the context

Read before asking anything:

- `specs/mission.md` - the phase must serve it
- `specs/tech-stack.md` - the phase must use it, or explicitly amend it
- the two or three most recent `specs/YYYY-MM-DD-*/` directories - for conventions, open decisions and anything this phase depends on
- `architecture/model.c4` and `architecture/views.c4` - what already exists structurally, and which phase tags are in use

## Step 3 - Propose the branch

```bash
git checkout -b phase/{phase-name}
```

Slug the phase name in kebab-case. Do not run it.

## Step 4 - Interview (mandatory)

One `AskUserQuestion` call, questions grouped to mirror the files being written:

- **Requirements** - where is the boundary? Offer concrete in/out splits, not an open prompt. Surface the decision that will be expensive to reverse. If the phase is being taken out of order, make the stub-versus-defer choice one of the questions.
- **Plan** - how should this be broken up and sequenced? Offer 2-4 real decompositions with trade-offs.
- **Validation** - what proves this is done? Automated tests, a manual walkthrough, a performance threshold, a demo.
- **Architecture** - which components does this phase introduce or rewire? Offer concrete shapes, and say which existing elements it will touch.

Give concrete options with trade-offs, informed by the context you just read. "What do you want?" is a wasted question.

## Step 5 - Write the spec

Get today's real date (`date +%F`) - never guess it. Create `specs/YYYY-MM-DD-phase-name/` and write four files.

**`requirements.md`**

```markdown
# {Phase-Name} - Requirements

## Scope

What this phase delivers. Specific enough to test.

## Out of Scope

- Explicitly deferred, so the boundary is on record

## Decisions

Configuration and architectural choices, each with the reason and the alternative rejected.

## Context

Why now, what it depends on, what depends on it. If this phase was taken out of roadmap order, say so here and name what was skipped.

## Stakeholder Notes

- **{User}** needs this to behave like X
- **{User}** has no requirements yet; this phase is plumbing only
```

**`plan.md`**

```markdown
# {Phase-Name} - Plan

## Tasks Group 1

1. Step 1.1
2. Step 1.2
3. Step 1.3

## Tasks Group 2

1. Step 2.1
2. Step 2.2
```

Step numbers carry the group number. Each group is a coherent unit that leaves the project in a working state, ordered so work can stop cleanly at any group boundary. More than ~5 groups means the phase is too big - propose splitting it in the roadmap.

**`validation.md`**

```markdown
# {Phase-Name} - Validation

## Definition of Done

The condition that makes this phase mergeable, in one or two sentences.

## Steps for Testing the Phase

1. `{command}` — expect {output}
2. {Observable manual check, with what you should see}

## Not Required

- What this phase does NOT have to prove
```

**Every step must be executable or observable.** A command with its expected output, or a manual check with what the user should see. "Works correctly" is not a step - if a step cannot fail, it is not validating anything.

**`architecture.md`**

```markdown
# {Phase-Name} - Architecture

## What This Phase Adds

- `{element-id}` — {what it is}, tagged `#{phase-tag}`
- `{element-id} -> {element-id}` — {relationship}

## Model Changes

Which elements and relationships were added or rewired in `architecture/model.c4`, and why.

## View

`architecture/views.c4` defines `view {phaseTag}`, filtered to this phase's tag plus its immediate
neighbours. Run `npx likec4 serve` and open it.

## Flow

A Mermaid sequence, flow or state diagram for this phase's behaviour - inline, so it renders on
GitHub. Structure belongs in the LikeC4 model, not here.
```

A phase that adds nothing structural still writes this file and says "no model changes" - that is a useful record, not an empty one.

## Step 6 - Stop

Present the four files and ask for sign-off. No code until the user approves.

---

## After sign-off

**Implement one task group at a time.** After each: run the project's tests and linters, report what passed, tick the group in `plan.md`. Update `architecture/model.c4` as soon as a new component appears, tagging it with this phase's tag - not at the end. If reality forces a deviation, update `plan.md` and `requirements.md` *first*, say so, then continue. If the user adds work mid-flight, add the task group and re-sync the other files so all four stay consistent.

**Validate** against `validation.md` step by step, reporting pass/fail per step. Do not claim done on a partial pass.

**Deep review before merge.** Spawn subagents to review the branch diff from independent angles - correctness against `requirements.md`, architecture and maintainability, security and failure modes - and report what doesn't hold up.

**Close out:** refresh `architecture/model.c4` and `architecture/views.c4` and run `npx likec4 validate`; add a dated entry to `CHANGELOG.md`; mark the phase complete in `specs/roadmap.md`; update `TODO.md` so *Now* and *Next* reflect what actually happened. Then propose:

```bash
git add -A && git commit -m "feat: {phase-name}"
git checkout main && git merge phase/{phase-name}
git branch -d phase/{phase-name}
```

Finally, check whether this work changed what remains - especially if the phase was taken out of order. If later roadmap phases should be combined, split or reordered, say so and propose the edit to `specs/roadmap.md`.