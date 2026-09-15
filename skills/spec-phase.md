# feature-spec skill

The skill will be registered at *[project_home]*/.claude/skills/*[skill name]*/SKILL.md

> I want to stop repeating the feature spec prompt. Use your skill creator to help me write a "feature spec" local skill. Here is the previous prompt:
>
> Find the next phase on `specs/roadmap.md` and make a branch, ask me about the feature spec.
>Create:
>
> - A new directory YYYY-MM-DD-feature-name under specs for this feature work
> - In there:
>   - `plan.md` as a series of numbered task groups.
>   - `requirements.md` for the scope, decisions, context
>   - `validation.md` for how to know the implementation succeeded and can be merged
>
> Refer to `specs/mission.md` and `specs/tech-stack.md` for guidance.
>
> Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.

## 2. Usage of the new skill

> Use your `/spec-phase` skill to work on a specificic roadmap phase. Otherwise the `/spec.dev` will take automatically the next phase.
