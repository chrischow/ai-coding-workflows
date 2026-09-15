# Milan Jovanovic — Engineering Approach to AI

[Link to video](https://www.youtube.com/watch?v=sGscFMQDGSg)

A walkthrough of Milan Jovanovic's personal workflow for using AI tooling (primarily Cursor) in .NET development, demonstrated with a real migration task: converting application query handlers from EF Core to raw SQL with Dapper.

## Core presumptions

- The developer already knows what they want to build and how to get there given enough time.
- AI is treated as an assistant, not a replacement for the developer.

## The workflow

1. **Plan first.** For anything more complex than a single file, the workflow starts with planning — not implementation. In Cursor this is "plan mode": give the AI a prompt, it scans the codebase, may ask clarifying questions, and produces a plan as a markdown document. The same effect is achievable with any AI tool (Copilot in VS Code/Visual Studio) by explicitly asking for a plan document before writing code.

2. **Be specific in prompts.** The prompt states exactly what to do and what NOT to do:
   - Migrate all query handlers ("ending with query handler") from EF Core to raw SQL with Dapper for performance.
   - Create a new class for fetching database connections using the `IDbConnection` interface.
   - Do not touch the command handlers at all.
   - Later nudges add specifics such as "use central package management (Directory.Packages.props)".
   - The AI model is often left on auto; Cursor picks what it considers optimal.

3. **Review the plan.** Check the plan before building — required steps, packages, files to be modified. This mirrors how a developer would plan a task by breaking it into smaller steps and implementing one by one; the workflow is just delegated to the AI.

4. **Review the generated code.** This is emphasised as critical. AI code is not assumed correct. Specific review findings in the video:
   - Unnecessary `Npgsql` NuGet package was added; it's already referenced transitively via the EF Core Postgres provider, so it was removed. Only Dapper is needed.
   - The SQL connection factory was fine, but could be improved by storing the connection in a variable and opening it before returning.
   - AI training data lags behind current library developments: the generated connection-creation code doesn't use the more modern `NpgsqlDataSource` approach. This is a shortcoming of AI tooling worth knowing.
   - DI registration was changed from scoped to singleton — the SQL connection factory is a stateless service.
   - Queries were correctly converted to SQL constants executed via Dapper.
   - Verbatim strings were replaced with modern multi-line raw string literals; once done once, the AI quickly replicates the style across the remaining queries.

5. **"Assumption is the mother of all messups."** Especially with AI-generated code, assume nothing and verify.

6. **Test properly.** Use a strong set of test cases run on demand, ideally integration tests against a real database instance — important with raw SQL so queries can actually execute and return correct values. In-memory databases risk subtle bugs creeping in. AI can write detailed test cases from a couple of examples.

7. **Run the code.** Build and verify it actually works. AI will not produce perfect code on the first try, but it gets roughly 90% of the way there, which is a significant time saving.

## Practical notes

- Cursor lacks C# Dev Kit support, so no built-in IntelliSense. Workarounds: run `dotnet build` in the terminal, or switch to Visual Studio to verify the code (e.g., catching a typo in a connection name).
- Alternative setups: Copilot in Visual Studio (or VS Code with Copilot, which solves the IntelliSense gap), including bringing your own model via an API key.

## Closing thoughts

- AI-generated code is here to stay and will improve.
- Open question: what happens to junior developers? Relying too much on AI may prevent them from learning the expensive lessons that come from exploring documentation, debugging for days, and fixing hard problems. It's unclear how those skills are acquired if AI produces the code.