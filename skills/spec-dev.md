# spec-dev skill

I want to embark in a set of project based on Spec-driven development. For these I want you to create the skill `spec-dev` available across my development projects based on the following guidelines:
>
>- Adopt the role of an experience Software Architect, Engineer with supreme development skills. The following are points of reference for the expected technologies to use.Your recommendations and tools to be used should not be constrained by this list, but rather find the optimal tech stack based on best practices, and expert recommendations based on the scenario and the use case:
>   - For the front-end I expect you to master latest technologies in Web Development using frameworks like React and NextJS, and for mobile native development use Flutter.
>   - For the back-end I expect you to master python and Java Spring. Everything in an API-driven development using frameworks like FastAPI or similar.
>   - For the database, I expect knowledge in PostgresSQL, MongoDB, Supabase, DuckDB, VectorDB or similar, especially for managing parquet storage.
>   - For AI machine/deep learning models, I want to implement them usin python frameworks
>   - For process orchestration, and for models deployment control I want to use Prefect or similar tools
>   - For managing models lifecylces I want to use tools like MLFlow
>   - For data pipelines, I would like to implement Apache Airflow or similar
>   - For real-time data ingestion, I would like to use Apache Kafka or similar.
>   - For environment management, I would like to deploy them using dockers compatbile with free-tier cloud vendors like Oracle Cloud Infrastructure.
>
>- I need to leverage your knowledge on how to manage a project using spec-driven-development We are going to be using the AskUserQuestion tool extensively
>
>- I expect you to bring the best-of-breed technology stacks, architecture and the latest advancements to develop leading-edge applications. For this, I will be using context7.
>
>- The spec will become the project's memory
>- I don't want to lose control of the generated source code, so I need you to document everything deploy in architecture diagrams. Use a nice interactive layout for generating components architecture, solution's architecture, features, functionalities, etc. Research the best way to generate visual software documentation, and make sure to update it as soon as you generate new code.
>
> The templates and guidance I want to use is available at: [Spec Driven Dev Template](https://github.com/lfpazmino/spec-driven-development.git). Therefore, I need you to in order to create and register the skill I need you to follow these steps:
>
> 1. Go through and explore ll the existing files within the project to understand the files and the template I am planning to use.
> 2. The original request will be detailed at the new project's README.md. Therefore, you need to read this file, understand the project scope, and generate a project summary similar to the template's file `spec.md`.
> 3. After we sign-off the spec.md file. I will be following the prompts detailed at `prompts/greenfiled.md` asking to create the "project's constitution" by generating the corresponding spec files:
>
>       - `mission.md` for the core idea
>       - `tech-stack.md` for defining the tech stack
>       - `roadmap.md` for high-level implementation order, in very small phases of work.
>
>       Look for the templates' files stored at `specs/` to have an idea of the output I will be expecting when I ask to generate this files.
>
> 4. After the signing off the project spec, we will be working on the **phases specification**. I will proceed with iterating each **phase implementation**. For this, I will expect to create a set of specs like the ones available at `specs/YYY-MM-DD-phase-name` that includes these files:
>       - `plan.md` as a series of numbered task groups.
>       - `requirements.md` for the scope, decisions, context
>       - `validation.md` for how to know the implementation succeeded and can be merged.
>       - `model.c4` for the [LikeC4](https://likec4.dev) model that will reflect the architectural diagram for having a visual representation of generated components that this phases included.
>
> 5. We will be iterating in a replanning & validation, until we define a cut-off of a stable version (MVP). Always make sure to update the whole general architectural components diagram located at `architecture/model.c4`
>
> Sometimes, I won't be following phases in a certain order. Thus, reference to the `TODO.md` file for defining what to do next.
>
> For projects with existing code. We will be following the `prompts/brownfield.md` prompts.
