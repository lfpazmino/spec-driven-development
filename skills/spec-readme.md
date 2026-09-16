# Spec README

A conversation that ends in a working prototype holds a project's requirements, scattered across dozens of turns and mixed in with throwaway implementation. This skill harvests that conversation into the two files `spec-dev` reads first: a `README.md` written as **requirements**, and a `TODO.md` of what's left.

This is the on-ramp. Output feeds `/spec-dev` Phase 1, which turns `README.md` into `spec.md` and then into roadmap phases.

## The one hard distinction

**What the user asked for is a requirement. What Claude built is one solution among many.**

A prototype is full of choices nobody ever decided - a library picked to move fast, an in-memory store because there was no database, a hardcoded value that stood in for config. If those land in the README as requirements, they become permanent by accident and the whole downstream spec inherits them.

So every line in the generated README carries provenance:

| Tag | Meaning |
| --- | --- |
| `[stated]` | The user said this, in some turn. Quote or paraphrase closely. |
| `[agreed]` | Claude proposed it, the user accepted it explicitly. |
| `[inferred]` | Neither - you are reading it off the prototype's behaviour. **Must be confirmed before it stays.** |

Nothing goes in untagged. An `[inferred]` line that survives the confirmation round gets re-tagged; one that doesn't gets deleted or demoted to an open question.

## Hard rules

1. **Never invent a requirement.** If the conversation doesn't establish it, it's an open question, not a requirement.
2. **Never run git.** Print commands for the user to run.
3. **Extract prototype code verbatim.** Do not refactor, rename, improve or "clean up" while extracting. The prototype's job is to be an honest record of what was proven to work. Fixes belong in the spec-driven build, not here.
4. **`AskUserQuestion` before writing to disk** - one round, on the inferred requirements and the scope boundary.
5. **Requirements get stable IDs** (`R-01`, `NFR-01`). `spec-dev` traces each one into `spec.md`, and traceability breaks without them.

---

## Step 1 - Sweep the conversation

Read the whole thread chronologically, oldest first. Do not summarize from the final state - the final state hides everything that was tried, rejected and corrected, and rejections are requirements too ("no, it has to work offline" is a requirement).

Collect as you go:

- **Asks** - anything the user wanted the thing to do.
- **Corrections** - every "no, actually..." and "that's not what I meant". These are the highest-signal requirements in the thread; the user only pushed back because something mattered.
- **Constraints** - performance, data volumes, latency, compliance, cost, existing systems, hosting limits.
- **Decisions** - who chose what, and whether the user actually weighed in or just let it pass.
- **Rejected paths** - what was tried and abandoned, and why. This prevents the rebuild from re-walking a dead end.
- **Domain context** - vocabulary, actors, real-world workflow the software models.
- **Artifacts** - every code block, file, artifact and attachment produced.

If the prototype was built in a *different* conversation, say so plainly: you cannot read another chat's transcript. Ask the user to run this skill inside that conversation, or to paste the key exchanges and attach the artifacts.

## Step 2 - Classify

Sort what you collected into:

- **Functional requirements** - what the system must do. Testable, one behaviour per line.
- **Non-functional requirements** - throughput, latency, availability, security, cost ceiling, operability.
- **Decisions already made** - with the reason, and whether they're still open to challenge.
- **Out of scope** - anything the user explicitly ruled out. Record it; an unstated exclusion gets rebuilt by accident.
- **Open questions** - what the prototype dodged and a real build cannot.

Then apply the honest test to every implementation detail in the prototype: *did the user ask for this, or did it just happen?* Tag accordingly. Expect most of the stack to be `[inferred]`.

## Step 3 - Extract the prototype

Write the generated code into `prototype/`, preserving file structure. Add `prototype/README.md`:

- how to run it (exact commands, actual dependency versions)
- what it demonstrates - the specific claims it proves
- what is throwaway: hardcoded values, mocked data, absent error handling, missing auth, no persistence
- what must **not** carry forward into the real build

This directory is evidence, not a foundation. Say so in the file.

## Step 4 - Confirm (mandatory)

One `AskUserQuestion` call, grouped:

- **Inferred requirements** - present the `[inferred]` lines that would most constrain the build and ask which are real requirements vs. prototype accidents. Offer concrete alternatives, not open prompts.
- **Scope boundary** - what's in v1 vs. deferred, given everything the conversation surfaced.
- **Continuation mode** - does the real build start clean with the prototype as reference only (greenfield), or grow out of the prototype code (brownfield)? This sets which `spec-dev` path runs next.

## Step 5 - Write the files

### `README.md`

```markdown
# {Project Name}

{One paragraph: what this is and who it's for. No implementation.}

## Input from stakeholders

- {Ask, close to the user's own words} `[stated]`
- {Another} `[stated]`

## Requirements

### Functional

| ID | Requirement | Provenance |
| --- | --- | --- |
| R-01 | The system MUST ... | `[stated]` |
| R-02 | Given X, when Y, then Z | `[agreed]` |

### Non-functional

| ID | Requirement | Provenance |
| --- | --- | --- |
| NFR-01 | Handles {volume} at {latency} | `[stated]` |

### Out of Scope

- {Explicitly ruled out, and by whom}

## Decisions already made

| Decision | Rationale | Settled? |
| --- | --- | --- |
| {choice} | {why} | Firm / Revisit at spec time |

## Rejected approaches

- **{Approach}** - tried, abandoned because {reason}. Do not revisit without new information.

## Prototype baseline

A working prototype exists in `prototype/`. It proves:

- {specific claim it validated}

It is **not** production scaffolding. Known gaps: {hardcoded config, no auth, mocked data, no persistence, no error handling}.

Run it: `{command}`

## Open questions

- {What a real build must settle that the conversation never did}

## Domain glossary

- **{Term}** - {what it means here}

## Next step

Build this with `/spec-dev` in **{greenfield | brownfield}** mode. `spec.md` traces every R-xx and NFR-xx above, and the roadmap breaks the work into phases.
```

Write the requirement lines in testable language - specific enough that each one passes or fails a test. "Fast" is not a requirement; "p95 under 200ms at 1k req/s" is. Where the conversation only produced "fast", make it an open question rather than inventing a number.

### `TODO.md`

`TODO.md` is the ordering authority for everything downstream - `spec-dev` reads it before `roadmap.md`. Write it in exactly this shape:

```markdown
# TODO

## Now

- {The single most valuable next piece of work}

## Next

- {Ordered, each traceable to an R-xx or an open question}
- Evaluate adjustments or refactoring required
```

`## Now` and `## Next` are `##`, not `###`, and the file's only `#` is `TODO` - not the project name. Seed *Now* from the largest gap between what the prototype proves and what the requirements demand, which is usually the thing the prototype faked.

Each entry names a unit of work that will become a **phase** once `spec-dev` writes the roadmap. Keep them phase-sized: a shippable, independently reviewable slice.

## Step 6 - Hand off

Report: how many requirements, how many still `[inferred]`, how many open questions, and which requirement you consider the riskiest. Then propose:

```bash
git add README.md TODO.md prototype/
git commit -m "docs: capture requirements and prototype baseline"
```

and tell the user to run `/spec-dev` next.

## What good output looks like

Someone who was never in the conversation can read `README.md` and know what to build, what was already decided and why, what not to build, and what still needs answering - without reading a single line of prototype code.

If the README only makes sense to someone who saw the chat, it isn't done.
