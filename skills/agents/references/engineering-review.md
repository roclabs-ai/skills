# Full-Codebase Engineering Quality Review

Applies when: performing a comprehensive engineering quality review of the whole project (not a diff review, not a plain bug hunt, not a security scan).

Execute the following instruction (verbatim):

> Perform a comprehensive Engineering Quality Review of the entire codebase.
>
> Do not do a Git diff review, do not only look for bugs, and do not only do a security scan. Treat the whole project as commercial-grade software that must be maintained long-term.
>
> First understand the entire codebase:
> - Read the project structure, config files, README, and core entry points
> - Identify the languages, frameworks, major dependencies, and tech stack
> - Understand module responsibilities, data flow, call relationships, and overall architecture
> - Read the key modules and representative code before drawing conclusions
>
> Review to the standard of a senior Staff Engineer / Principal Engineer.
>
> Focus areas:
>
> 1. Code quality
> - Is the code concise, clear, and easy to understand
> - Is there duplicated code
> - Are there meaningless wrappers
> - Is there over-engineering
> - Is there abstraction for abstraction's sake
> - Are there places that could be implemented more simply
> - Does it follow the recommended idioms of the language's ecosystem
>
> 2. Modern engineering practices
> - Does it use currently recommended language features and modern idioms
> - Are there outdated APIs, legacy patterns, or discouraged practices
> - Could framework capabilities replace custom code
> - Are there parts that should be refactored to modern idioms
>
> 3. Third-party libraries and ecosystem usage
> - Does it reimplement problems already solved by mature libraries
> - Should mature community solutions replace self-maintained code
> - Do the libraries in use match mainstream ecosystem practice
> - Do home-grown solutions increase maintenance cost
>
> 4. Architecture
> - Are module responsibilities clear
> - Are dependency directions sensible
> - Is there severe coupling
> - Are there circular dependencies
> - Are there wrong abstractions
> - Will it rot as the project grows
> - Does it fit long-term evolution needs
>
> 5. Maintainability
> - Can a new developer understand the code easily
> - Is naming accurate
> - Is the API design idiomatic
> - Is the type design sound
> - Is error handling consistent
> - Is configuration management sensible
> - Are logging and observability sensible
>
> 6. Engineering standards
> - Is the test structure sensible
> - Does test coverage follow best practices
> - Are there designs that are hard to test
> - Is there hidden technical debt
> - Are there implementations likely to cause future problems
>
> 7. Simplicity principle
> Focus on:
> - Can code be deleted
> - Can standard capabilities replace complex implementations
> - Can mature libraries replace custom implementations
> - Can code and maintenance complexity be reduced
>
> Do not raise meaningless issues just to pad the count.
>
> If the code is already sound, explicitly explain why it is sound.
>
> Final output:
>
> ## 1. Overall Project Understanding
> Describe the architecture, module relationships, and main design as you understand them.
>
> ## 2. Engineering Quality Assessment
> Assess whether the current code meets the standard of an excellent engineering team.
>
> ## 3. What Is Done Well
> List designs worth keeping.
>
> ## 4. Problems That Must Be Fixed
> List problems that hurt long-term maintenance, extension, and quality.
>
> ## 5. Recommended Improvements
> Give approaches better aligned with modern best practices.
>
> ## 6. Technical Debt Analysis
> Point out technical debt that already exists or is forming.
>
> ## 7. Priority Ranking
> Classify as P0, P1, P2, P3.
>
> Every problem must include:
> - File or directory location
> - What is wrong with the current design
> - Why it is a problem
> - Recommended fix
> - Whether it is worth fixing immediately
>
> Do not modify code directly; output the review results only.
