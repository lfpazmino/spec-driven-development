<!-- 
Spec-Driven Development Prompts 
for Greenfield projects
-->

# Prompts

## 1. Specify the kick-off

> We are writing` {MyProject} ` with this goal. Look in the README.md for input from stakeholders.

## 2. Create the constitution

> Let's create a "constitution" in a specs directory:
>
>- `mission.md` for the core idea
>- `tech-stack.md` for defining the tech stack
>- `roadmap.md` for high-level implementation order, in very small phases of work.
>
>Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.

<!-- Don't forget to commit the project at every checkpoint !-->
> Just show instructions to commit, merge with the main branch and delete this branch. Do not execute any git commands

## 3. Add any missing section e.g. not target audience

> Add a target audience to the mission

## 4. Feature Specification

> Find the next phase on specs/roadmap.md and make a branch, ask me about the feature spec.
> Create:
>
> - A new directory YYYY-MM-DD-feature-name under specs for this feature work
> - In there:
>
>   - `plan.md` as a series of numbered task groups.
>   - `requirements.md` for the scope, decisions, context
>   - `validation.md` for how to know the implementation succeeded and can be merged
>
> Refer to specs/mission.md and specs/tech-stack.md for guidance.
>
>Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.

<!-- Any missing task -->
> Add a task group to the plan to have a minimal {MyProject} home page and update the rest of the feature spec to be in sync.

## 5. Feature implementation

> Implement the remaining task group
>
> Update the {YYYY-MM-DD- feature-1}/plan.md and implement a ... (adjustments)
>
> Mark this specs/roadmap.md phase as complete
>
> Just show instructions to commit, merge with the main branch and delete this branch. Do not execute any git commands

## 6. Replanning & Validation

> Update this tech-stack.md to capture that we want to use Vitest tests for validation and write a script in package.json.
>
> Update existing specs and code to reflect these testing changes.
>
> Write a new test suite using the specified testing framework.
>
> The product's web UI should follow responsive design. Update the product specs and all feature specs to reflect this, as well as any code.

Create a skill for a repeatable work:
> I want to keep a CHANGELOG.md in the project root, with headings for dates. If no changelog, examine git commits and add bullets for each date. Then, as we work, we will manually invoke this skill before merging. Help me write a skill for this.
>
> Use your changelog skill to update the changelog.
>
> Just show instructions to commit, merge with the main branch and delete this branch. Do not execute any git commands

Analyze the roadmap and check if after this iteration it still applies, otherwise combine/create new phases accodingly:
> Go to the roadmap.md and combine phases 2-3-4-5 into a new phase 2.

## 7. Iterate to the next feature

Start with a clean context `/clear`

Commit and merge the corresponding branch:
> Find the next phase on specs/roadmap.md and make a branch, ask me about the features spec.
>
> Create:
>
> - A new directory YYYY-MM-DD-feature-name under specs for this feature work
> - In there:
>   - `plan.md` as a series of numbered task groups
>   - `requirements.md` for the scope, decisions, context
>   - `validation.md` for how to know the implementation succeeded and can be merged
>
> Refer to specs/mission.md and specs/tech-stackmd for guidance.
>
> Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.

Implement this feature by making the size of the feature manageable:
> Implement this feature

Ask agents to refactor if required, or to follow specific coding guidelines

Use subagents for validation to avoid cognitive debt:

> Do a deep review: Spawn multiple subagents to go through all the changes on this branch from three different perspectives and see if anything doesn't make sense, could be better, etc.

Call the changelog skill
> Use your changelog skill to update the changelog with work done on this branch.

## 8. The MVP

Cut-off feature development for a MVP stable version

> Go through all the features on specs/roadmap.md, make a mvp branch, and ask me about the feature specs needed to complete an MVP.
>
> Create:
>
> - A new directory under specs for this feature work
> - In there:
>   - `plan.md` for the task list
>   - `requirements.md` for the scope, decisions, context
>   - `validation.md` for how to know the implementation succeeded and can be merged
>
> Refer to specs/mission.md and specs/tech-stack.md plus the existing feature specs for guidance.
>
> Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.

Implement the MVP plan
> Implement this plan.

Validate the specs:
>Based on the MVP, did you find anything that needs clarification in the specs?
