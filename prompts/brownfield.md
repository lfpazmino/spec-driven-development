<!-- 
Spec-Driven Development Prompts 
for Brownfield projects / Legacy Support
(existing codebase)
-->

# Prompts

## 1. Establish the context for a constitution generation

Work in a clean session, and in a new project directory. It should apply reverse engineering to the existing codebase

> We have an {My_Project} project, [describe the project].
>
> Look in README.md for input from stakeholders. Make a constitution in a specs directory:
>
> - `mission.md`
> - `tech-stack.md`
> - `roadmap.md` should be based on the TODO.md for high-level implementation order, in very small phases of work.
>
> Interview me about mission, target audience, tech stack gaps.
>
> Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.

## 2. Execute the implementation

> Implement the plan.

## 3. Find the next phase to implement

Defined in TODO.md file

> I want to stop repeating the phase spec prompt. Use your skill creator to help me write a "phase spec" local skill. Here is the previous prompt:
>
> Find the next phase on specs/roadmap.md and make a branch, ask me about the phase spec.
> Create:
>
> - A new directory YYYY-MM-DD-phase-name under specs for this phase work
> - In there:
>   - `plan.md` as a series of numbered task groups.
>   - `requirements.md` for the scope, decisions, context
>   - `validation.md` for how to know the implementation succeeded and can be merged
>   - `model.c4` for the [LikeC4](https://likec4.dev) model that will reflect the architectural diagram for having a visual representation of generated components that this phases included.
>
>Refer to specs/mission.md and specs/tech-stack.md for guidance.
>
>Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.
>
> Align the specs/roadmap.md with the TODO.md file

## 4. Implement the phase

> Implement the phase's plan
>
>At the end mark the Phase as completed in /specs/roadmap.md
>
> Just show instructions to commit, merge with the main branch and delete this branch. Do not execute any git commands
