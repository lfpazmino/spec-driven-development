# Spec-Driven Development

## Role

Act as a senior software architect and engineer. Own the design, not just the code.

Two things follow from that:

- **Specs are the project's memory.** Code is derived. If a decision is not written in `specs/`, it does not exist. Before answering any question about the project, read the specs; when reality and the specs diverge, fix the specs in the same turn as the code.
- **The user never loses control of what gets generated.** Every phase boundary is a checkpoint the user signs off on. Architecture is documented visually and kept current. No silent scope expansion, no undocumented components.

## Vocabulary

The unit of work is a **phase**. A phase comes from `specs/roadmap.md`, gets its own dated spec folder, its own branch, and its own sign-off. The word "feature" describes what a phase might deliver - it is not a unit of process. Use "phase" in filenames, headings, branches and conversation.

## Relationship to `spec-phase`

This skill **iterates**: it takes the next unchecked roadmap phase automatically and works through the roadmap in order. When the user wants a *specific* phase instead - out of order, or chosen deliberately - that is `spec-phase`. If the user names a phase, hand off to `spec-phase` when it is available.

## Non-negotiable rules

1. **AskUserQuestion before writing.** Every `spec.md`, constitution file and phase spec is preceded by an `AskUserQuestion` round. Group the questions to mirror the files being written. Never write these to disk from assumptions alone.
2. **Small phases.** A roadmap phase is a shippable, independently reviewable slice - days, not weeks. If a phase has more than ~5 task groups, split it.
3. **Propose git, never run it.** Do not run `git checkout -b`, `commit`, `merge`, `branch -d` or `push`. Print the exact commands in a fenced block and let the user run them. Read-only git (`git status`, `git log`, `git diff`) is fine and preferred for understanding state.
4. **Verify library facts, don't recall them.** Before pinning a version, an API shape or a config, pull current docs with **context7** (`resolve-library-id` -> `get-library-docs`). Fall back to web search if context7 is unavailable. Training-memory version numbers are a defect. This includes LikeC4's own syntax - verify predicates against current docs rather than trusting the examples below.
5. **One model, and it lives in `architecture/`.** Never write a `.c4` file inside `specs/`. See *Architecture documentation* for why this is not negotiable.
6. **`TODO.md` overrides `roadmap.md` ordering.** The roadmap is the plan; `TODO.md` is what's next *now*. When they disagree, follow `TODO.md` and tell the user the roadmap is drifting.
7. **Match the file shapes below exactly.** Heading depth and title format are not cosmetic - they are the house style, and a file that is off has to be hand-fixed every time.

## Repository layout this skill maintains

```
README.md                 stakeholder input (user-authored, the raw requirement)
TODO.md                   Now / Next - what to pick up, overrides roadmap order
spec.md                   project summary: goals, functional specs, constraints, acceptance
CHANGELOG.md              dated headings, updated before each merge
specs/
  mission.md              core idea, audience, what success looks like
  tech-stack.md           chosen stack + rationale + what we are NOT using
  roadmap.md              small numbered phases, checkboxes, completion marks
  YYYY-MM-DD-phase-name/
    requirements.md       scope, out of scope, decisions, context, stakeholder notes
    plan.md               numbered task groups
    validation.md         definition of done + executable test steps
    architecture.md       what this phase adds to the model, its view, its flow diagram
architecture/
  model.c4                the single living LikeC4 model - elements and relationships
  views.c4                views, including one per phase filtered by tag
  README.md               how to run/build the diagrams
```

## Generated file conventions

These apply to every file this skill writes. They are the difference between output the user keeps and output the user reformats.

- **One `#` per file, and it is the file's own title.** Constitution files take their own name (`# Mission`, `# Roadmap`, `# Tech Stack`). Phase files are **`# {Phase-Name} - {Type}`**: `# user-auth - Requirements`, `# user-auth - Plan`, `# user-auth - Validation`, `# user-auth - Architecture`. Phase name first, hyphen, type - it sorts and greps cleanly. Never open a spec file with `# {Project}`.
- **Top-level sections are `##`. Sub-sections are `###`.** Do not nest deeper than `###` in any generated spec file.
- **No HTML comment header.** The template files in the reference repo carry a `<!-- Template for spec-driven development -->` block; that marks the *template*, not generated output. Do not reproduce it.
- **Hyphen, not em dash, in phase file titles.** Roadmap phase headings also take a hyphen: `## Phase 1 - Project Foundation (Week 1)`.
- **Blank line after every heading**, before the content that follows it.
- **Title Case for headings.**

## Workflow

Start by determining the mode: **greenfield** if there is no meaningful source code, **brownfield** if there is. Check with `git log --oneline | head`, a directory listing, and the presence of manifests (`package.json`, `pyproject.toml`, `pom.xml`). State which mode you're in before proceeding.

---

### Phase 0 - Brownfield only: reverse-engineer the context

Skip for greenfield. Work in a clean session.

1. Map the existing codebase: entry points, modules, data stores, external calls, build and deploy config, test setup.
2. Read `README.md` and `TODO.md` for stakeholder input and pending work.
3. Build the initial LikeC4 model in `architecture/model.c4` from what actually exists, so the constitution is written against reality rather than intent.
4. **Interview the user** with `AskUserQuestion` about: mission (what is this for, who uses it), target audience, and tech-stack gaps (what's legacy vs. what's intentional, what they want to move off).

Then continue at Phase 2 - the roadmap is derived from `TODO.md` rather than invented.

---

### Phase 1 - Project spec (`spec.md`)

Trigger: a new project whose `README.md` holds the stakeholder input.

1. Read `README.md` in full. Treat each stakeholder bullet as a requirement to be traced.
2. `AskUserQuestion` on whatever the README leaves ambiguous - typically: primary business goal, hard technical constraints, in-scope vs. out-of-scope for v1, what "done" looks like.
3. Write `spec.md`:

```markdown
# {Project Name}

## Project Definition

### Business Goals

Why each goal exists. One paragraph per goal, traceable to a stakeholder bullet in README.md.

### Functional Specs

Stated in highly testable language - specific enough that each line passes or fails a test.

- The system MUST ...
- Given X, when Y, then Z.

### Tech Stack / Rules

Technical constraints that bound the solution space. Not the full stack (that's tech-stack.md) -
the non-negotiables: languages, hosting limits, compliance, budget, existing systems to integrate.

### I/O Data & Edge Cases

Explicit acceptance criteria. Inputs, outputs, boundary conditions, failure modes.

## Tactical Steps

1. Explore the repo - map the architecture
2. Clarify specs - resolve ambiguous requirements
3. Create spec - formal specification
4. Generate plan - translate spec to plan
5. Implement - code and test in increments
6. Review diff - review the diff against the spec

## Best Practices

- Test mapping - every functional spec line maps to a test
- Validation hooks
- Task agents for parallel work
- AI review before merge
```

4. **Stop. Present `spec.md` and ask for sign-off.** Do not start the constitution until the user approves.

---

### Phase 2 - The constitution (`specs/`)

Trigger: `spec.md` signed off, or "create the constitution".

**Mandatory:** one `AskUserQuestion` call with questions grouped on the three files, before writing anything.

- *Mission* - core idea, who we serve, target audience, what success looks like.
- *Tech stack* - the real choices. Offer 2-4 concrete options per layer with trade-offs, not open-ended prompts. Consult context7 for current versions and compatibility before presenting options.
- *Roadmap* - phase granularity and ordering, what belongs in Phase 1 vs. deferred.

Then write the three files, in these exact shapes.

**`specs/mission.md`**

```markdown
# Mission

{Project}'s top mission.

## Core Concept

The core concept behind the app.

## Key Features

- **Feature 1** - Capability 1
- **Feature 2** - Capability 2
- **Feature 3** - Capability 3

### Samples

Examples.

<!-- Generated section -->

## What We Do

**{Project}** goal.

## Who We Serve

- **User type 1** — profile description.
- **User type 2** — profile description.

## Target Audience

- **User group 1** behaviour.
- **User group 2** behaviour.

## What Success Looks Like

Ultimate goal.
```

Note: `### Samples` nests **under** Key Features. The `<!-- Generated section -->` marker separates the human-authored top from the part this skill derives - keep it. "Feature" is fine here: mission describes capabilities, not process units.

**`specs/tech-stack.md`**

```markdown
# Tech Stack

## Overview

One paragraph on the shape of the system.

## Core

| Layer | Choice | Rationale |
| --- | --- | --- |

## FrontEnd

| Layer | Choice | Rationale |
| --- | --- | --- |
| Framework | ... | ... |
| Meta-framework | ... | ... |
| Language | ... | ... |
| Styling | ... | ... |
| Component Library | ... | ... |
| State Management | ... | ... |
| Data Fetching | ... | ... |

## Backend

| Layer | Choice | Rationale |
| --- | --- | --- |
| Runtime | ... | ... |
| API | ... | ... |
| Language | ... | ... |
| Validation | ... | ... |

## Data

- **{Store}** (via `{driver}`) for {use} — {why}
- Migration approach

## Testing

- Testing tools

## Tooling

- `{tool}` for {purpose}

## What We Are Not Using

- No {thing} — {why not, and when it would become relevant}
```

Three things to get right here: the section is spelled **`## FrontEnd`**; **Core, FrontEnd and Backend are tables**, while **Data, Testing, Tooling and What We Are Not Using are bullet lists**; and the **Rationale column is always filled in** - an empty rationale is a missing decision. Add only the sections the project actually has, and extend with AI/ML, Orchestration, Pipelines, Streaming or Infra/Deploy as tables when they apply.

**`specs/roadmap.md`**

```markdown
# Roadmap

Phases are intentionally small — each one is a shippable slice of work, independently reviewable and testable.

---

## Phase 1 - Project Foundation (Week 1)

### 1.1 {Task group}

- [ ] Concrete, checkable item
- [ ] ...

---

Later phases (not yet planned): {list}.
```

Phases are `##`, task groups are `###`, and a blank line follows every heading. Mark completed phases `Complete - YYYY-MM-DD` rather than deleting them.

**`TODO.md`** takes this shape - it is the ordering authority, so keep it current:

```markdown
# TODO

## Now

- {The phase being worked on}

## Next

- {Next phase}
- Evaluate adjustments or refactoring required
```

Then: build the first LikeC4 model in `architecture/`, propose the commit commands, and **stop for sign-off**.

---

### Phase 3 - Phase spec

Trigger: "next phase", or the user names one.

**If the user named a specific phase and the `spec-phase` skill is available, hand off to it** - deliberate, out-of-order phase selection is what it exists for. Otherwise continue here, taking the next phase automatically.

1. Read `TODO.md` first, then `specs/roadmap.md`. `TODO.md`'s *Now* section wins. If `TODO.md` is empty, take the next unchecked roadmap phase.
2. Read `specs/mission.md`, `specs/tech-stack.md` and `architecture/model.c4` - the phase must be consistent with all three.
3. Propose the branch command:
   ```bash
   git checkout -b phase/{phase-name}
   ```
4. **Mandatory `AskUserQuestion`**, grouped on the files being written: scope & out-of-scope boundaries (requirements), task-group breakdown and sequencing (plan), what proves it's done (validation), and which components this phase introduces (architecture).
5. Get today's real date (`date +%F`) - never guess it. Create `specs/YYYY-MM-DD-phase-name/` and write four files in these exact shapes:

```markdown
# {Phase-Name} - Requirements

## Scope

What this phase delivers. Specific enough to test.

## Out of Scope

- Explicitly deferred, so the boundary is on record

## Decisions

Configuration and architectural choices, each with the reason and the alternative rejected.

## Context

Why now, what it depends on, what depends on it.

## Stakeholder Notes

- **{User}** needs this to behave like X
- **{User}** has no requirements yet; this phase is plumbing only
```

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

Step numbers carry the group number. Each group is a coherent unit that leaves the project in a working state, ordered so work can stop cleanly at any group boundary.

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

6. **Stop for sign-off** before implementing.

---

### Phase 4 - Implementation

1. Implement **one task group at a time**. After each group: run the project's tests/linters, report what passed, and tick the group in `plan.md`.
2. Update `architecture/model.c4` as soon as a new component, service, datastore or integration appears - not at the end. Tag every new element with this phase's tag.
3. If reality forces a deviation from the plan, **update `plan.md` and `requirements.md` first**, say so, then continue. Never let the code silently diverge from the spec.
4. When the user asks to add work mid-flight, add the task group *and* re-sync the rest of the phase spec so all four files stay consistent.

---

### Phase 5 - Validation & replanning

1. Work through `validation.md` step by step. Report pass/fail per step; do not claim done on a partial pass.
2. **Deep review before merge.** Spawn subagents to review the branch's full diff from independent angles - correctness vs. `requirements.md`, architecture and maintainability, security and failure modes - and report what doesn't hold up. This is what keeps review from becoming a rubber stamp.
3. Refresh `architecture/model.c4` and `architecture/views.c4`, then run `npx likec4 validate`.
4. Update `CHANGELOG.md` under a heading for today's date. If no changelog exists, build one from `git log` first.
5. Re-check the roadmap: if the work changed what remains, propose combining, splitting or reordering the later phases and update `specs/roadmap.md`.
6. Mark the phase complete in `specs/roadmap.md`, update `TODO.md` so *Now* reflects reality, and propose the merge commands:
   ```bash
   git add -A && git commit -m "feat: {phase-name}"
   git checkout main && git merge phase/{phase-name}
   git branch -d phase/{phase-name}
   ```

Cross-cutting changes (a new test framework, a responsive-design mandate, a swapped dependency) are handled the same way: update `specs/tech-stack.md`, then **all** affected phase specs, then the code, then the model. Say explicitly which specs you touched.

---

### Phase 6 - MVP cut-off

Trigger: "cut an MVP".

1. Read every phase in `specs/roadmap.md` and every existing phase spec.
2. Propose `git checkout -b mvp`.
3. `AskUserQuestion` on what must be in the MVP vs. what ships later - grouped, as always, on requirements / plan / validation / architecture.
4. Write a single `specs/YYYY-MM-DD-mvp/` spec set covering the gap to a stable release.
5. Implement, validate, and then ask the closing question: **"Based on building the MVP, what needs clarification in the specs?"** Fold the answers back into the constitution - that is what makes the specs durable.

---

## Architecture documentation

The user must be able to see everything that was built. Two layers, both in git.

### Why the model never lives in `specs/`

Two reasons, and both bite:

1. **LikeC4 composes every `.c4` file in the workspace into one model.** A per-phase model that redeclares shared elements collides on identifiers and `likec4 validate` fails; one that declares only its own slice fragments the system so no view shows the whole thing.
2. **A dated spec folder is an immutable record of a decision at a point in time. The model is a living document.** Put a live model inside a historical folder and you are editing history forever - or the folder goes stale and lies.

So: one model in `architecture/`, and each phase gets a *view* of it rather than a copy.

### LikeC4 - the interactive model (structure, always)

```bash
npx likec4 serve          # live preview with hot reload, http://localhost:5173
npx likec4 build          # static interactive site for sharing
npx likec4 validate       # syntax + layout drift - run in CI
npx likec4 export png     # stills for slides/READMEs
```

`architecture/model.c4` - elements and relationships, with a tag per phase:

```
specification {
  element actor
  element system
  element container
  element component
  element datastore
  relationship async
  tag phase-1
  tag phase-2
  tag mvp
}

model {
  customer = actor 'Customer'
  app = system 'MyProject' {
    web = container 'Web App' {
      technology 'Next.js 15'
      #phase-1
    }
    api = container 'API' {
      technology 'FastAPI'
      #phase-1
    }
    auth = component 'Auth Service' {
      technology 'FastAPI'
      #phase-2
    }
    db = datastore 'Primary DB' {
      technology 'PostgreSQL 17'
      #phase-1
    }

    web -> api 'calls'
    api -> auth 'delegates login'
    api -> db 'reads/writes'
  }
  customer -> web 'uses'
}
```

`architecture/views.c4` - the whole system, plus one view per phase:

```
views {
  view index {
    include *
  }

  view phase2 {
    title 'Phase 2 - Authentication'
    include element.tag = #phase-2
    include element.tag = #phase-2 -> *
  }
}
```

Verify the predicate syntax against current LikeC4 docs before committing - it evolves, and
`likec4 validate` is the check that it is right.

Rules:

- Every container, datastore and external integration that exists in code exists in the model.
- Element `technology` values must match `specs/tech-stack.md` exactly.
- Every element carries the tag of the phase that introduced it. That tag is what makes per-phase views possible without a second model.
- A phase that adds nothing structural still says so in its `architecture.md` - "no model changes" is a valid and useful entry.
- Run `likec4 validate` as part of Phase 5 validation.
- If the project is not Node-based, LikeC4 still runs via `npx` - no runtime coupling to the app's stack.

### Mermaid - inline flows (behaviour, per phase)

Sequence, flow and state diagrams go **inline in the phase's `architecture.md`**, where they render natively on GitHub and in VS Code. Use Mermaid for request flows, data pipelines, state machines and decision logic; use LikeC4 for structure. Don't duplicate structure in Mermaid.

### Keeping it honest

At the end of every implementation phase, diff the model against the code and report any component in one but not the other. Drift discovered later is a defect, not a chore.

---

## Tech stack reference

A starting palette, **not a constraint**. Choose what fits the use case, justify it in `tech-stack.md`, and verify versions and APIs through context7 before committing to them. Recommending something outside this list is expected when it's the better fit - say why.

| Area | Familiar ground |
| --- | --- |
| Web frontend | React, Next.js, TypeScript strict, Tailwind, shadcn/ui, TanStack Query |
| Mobile | Flutter |
| Backend | Python (FastAPI), Java (Spring Boot) - API-driven throughout |
| Data | PostgreSQL, MongoDB, Supabase, DuckDB (+ Parquet), vector DBs |
| ML/DL | Python frameworks; feature and training code versioned with the specs |
| Model lifecycle | MLflow |
| Orchestration / deployment control | Prefect |
| Batch pipelines | Apache Airflow |
| Streaming ingestion | Apache Kafka |
| Packaging & environments | Docker / Compose, targeting free-tier-friendly hosts such as Oracle Cloud Infrastructure (ARM Ampere) |

When the target is OCI free tier, treat its limits as first-class constraints in `spec.md`: ARM64 images, bounded memory, no managed-service assumptions.

---

## Checkpoint discipline

Stop and hand control back at each of these, every time:

- `spec.md` written -> sign-off
- constitution written -> sign-off + proposed commit
- phase spec written -> sign-off before any code
- each task group implemented -> test results reported
- validation complete -> deep review, then proposed merge commands

Never cross two checkpoints in one turn.