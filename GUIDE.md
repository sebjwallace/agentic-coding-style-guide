# Coding Style Guide: Overview

The whole guide in brief, for AI coding agents working in TypeScript/JavaScript codebases. The principles are general; the examples are TypeScript. Keep this picture in mind throughout a task, and use the [reading map](#what-to-read-next) to open the detail you need.

## The idea

Code is a **model** of the thing it represents. Its job is to express the **essential complexity** of the problem and remove as much **accidental complexity** as possible, while staying consistent with the codebase it lives in.

This guide describes the **target shape** of the code you produce. It is not a checklist to satisfy line by line while you are still exploring. However you reach a working solution, this is what it must be refined into.

## Two rules that always apply

- **Never decide a trade-off silently.** When principles conflict, or a decision has meaningful trade-offs, raise it with the user: present the options, recommend one, let the user decide, and record the decision. See [Section 3](sections/03-trade-offs-and-decision-log.md).
- **Never quietly bypass a constraint.** No `@ts-ignore`, disabled lint rules or tests, or skipped layers to make a change pass. Fix the cause. If the constraint seems wrong, raise it. See [Section 17](sections/17-constraints-no-shortcuts.md).

## The principles

### Foundations

- **[1. Core philosophy](sections/01-core-philosophy.md).** Reduce noise. Every line must be justified and every branch questioned. A good solution feels like common sense and takes little time to explain. The first working solution is rarely the simplest, and finding the simple one is worth the work.

### Working within a codebase

- **[2. Understand the codebase before writing](sections/02-understand-the-codebase.md).** Discover its patterns, constraints, principles, optimisation goals, framework idioms, and anti-patterns. Follow its good patterns so that nothing surprises people who know the code. Raise anti-patterns and bugs rather than copying them or silently fixing them.
- **[3. Trade-offs and the decision log](sections/03-trade-offs-and-decision-log.md).** The user holds context you don't, so the user decides. A decision log stops the same debate from recurring and explains why the code looks the way it does.
- **[17. Respect constraints, no shortcuts](sections/17-constraints-no-shortcuts.md).** Simplicity does not mean cutting corners. Constraints often encode lessons, compliance needs, or agreements that aren't visible in the code. Understand why each one exists.

### Modelling the domain

- **[4. Speak the language of the domain](sections/04-domain-language.md).** Names express real concepts and what things mean, not how they are implemented. Avoid `data`, `item`, `handler`, `manager`. Use one term per concept across code, types, tests, and UI.
- **[5. The type system is a design tool](sections/05-types-as-design-tool.md).** Use types to model the domain so that they tell its story. Make illegal states unrepresentable, give domain concepts their own types, and parse untrusted data once at the boundary, then trust the types inside.
- **[6. Model discrete states, then match on them](sections/06-discrete-states.md).** Use named variants rather than tangles of booleans and optionals. Match on them exhaustively. Classify which situation you are in, then act on it separately. Replace boolean parameters with named options.
- **[14. Errors are part of the domain](sections/14-errors-as-domain.md).** Model expected failures as values with named variants, such as a `Result`. Unexpected failures may throw. This is a default stance: follow the codebase's existing convention, and raise any conflict with it.

### Readability

- **[7. Code that explains itself](sections/07-self-explaining-code.md).** Code should read like natural language. Write declaratively, and break logic into well-named constants so that the final expression reads like a sentence. Comments explain *why*, not *what*.

### Structure

- **[8. Functional by default](sections/08-functional-by-default.md).** Write pure functions and don't mutate inputs. Use a functional core with an imperative shell, pushing side effects to the edges. If the codebase follows another paradigm, raise the trade-off rather than imposing this one.
- **[9. Decompose to mirror the domain](sections/09-decomposition-and-composition.md).** Code structure reflects domain structure. Each piece can be understood in isolation, and the whole is understood through how the pieces compose.
- **[10. Encapsulation and minimal interfaces](sections/10-encapsulation-and-interfaces.md).** Reveal only what is needed. Every export and parameter is a promise and a coupling point, so hide internals and accept only what a function uses.
- **[11. Balancing DRY and WET](sections/11-dry-and-wet.md).** Share code when it represents the same knowledge and changes for the same reason. Keep it separate when it merely looks similar today.

### Simplicity

- **[12. Minimalism and elegance](sections/12-minimalism-and-elegance.md).** Do as much as you can with as little code as possible, without becoming clever or cryptic. No workarounds and no over-engineering. The measure is the effort to understand, not the line count.
- **[13. Complexity must be earned](sections/13-earned-complexity.md).** Every line, branch, parameter, abstraction, and dependency must justify itself. Remove defensive branches that the types already rule out. If a case shouldn't be possible, change the type so that it can't be.

### Change

- **[15. Testability](sections/15-testability.md).** If code is hard to test, the design is telling you something. Test behaviour through a small public interface, not internals. Mock only at true system boundaries.
- **[16. Adaptability and evolvability](sections/16-adaptability.md).** Code should be easy to change. Each concept has one home, so that a change in one place stays in one place. This follows from the principles above.

### Process

- **[18. Working process](sections/18-working-process.md).** You don't have to write perfect code in one go. Explore freely with the target shape in mind, then refactor to it once the problem is solved.
- **[19. Self-review checklist](sections/19-self-review-checklist.md).** Walk through it before presenting code as complete.
- **[Appendix: Decision log template](sections/appendix-decision-log-template.md).** The format for recording decisions the user has made.

## What to read next

### By stage of the work

These stages follow the working process in [Section 18](sections/18-working-process.md).

| Stage | What you are doing | Read |
|---|---|---|
| 1. Discover | Studying the codebase and its constraints | [02](sections/02-understand-the-codebase.md), [17](sections/17-constraints-no-shortcuts.md) |
| 2. Model | Sketching domain types, states, and failures | [04](sections/04-domain-language.md), [05](sections/05-types-as-design-tool.md), [06](sections/06-discrete-states.md), [14](sections/14-errors-as-domain.md) |
| 3. Explore | Writing whatever proves the approach | This overview is usually enough; [18](sections/18-working-process.md) for the process |
| 4. Raise | Surfacing trade-offs, constraints, anti-patterns, bugs | [03](sections/03-trade-offs-and-decision-log.md) |
| 5. Refine | Refactoring to the target shape | [07](sections/07-self-explaining-code.md), [08](sections/08-functional-by-default.md), [09](sections/09-decomposition-and-composition.md), [10](sections/10-encapsulation-and-interfaces.md), [11](sections/11-dry-and-wet.md), [12](sections/12-minimalism-and-elegance.md), [13](sections/13-earned-complexity.md); only those relevant to the code in hand |
| 5. Refine (tests) | Writing tests, checking how easily the code will change | [15](sections/15-testability.md), [16](sections/16-adaptability.md) |
| 6. Self-review | Checking the work before presenting it | [19](sections/19-self-review-checklist.md) |
| 7. Record | Logging what the user decided | [Appendix](sections/appendix-decision-log-template.md) |

### By situation

| If you are... | Read |
|---|---|
| New to a codebase, or adding a new kind of thing to it | [02](sections/02-understand-the-codebase.md) |
| Finding that the codebase conflicts with this guide, or principles conflict with each other | [03](sections/03-trade-offs-and-decision-log.md) |
| Struggling to name something | [04](sections/04-domain-language.md) |
| Designing a data shape, or using optional fields and primitives for domain values | [05](sections/05-types-as-design-tool.md) |
| Handling API responses, user input, files, or environment values | [05](sections/05-types-as-design-tool.md) (5.3) |
| Writing `if`/`&&`/`\|\|` tangles, status flags, or boolean parameters | [06](sections/06-discrete-states.md) |
| Facing a long expression, or about to write a comment | [07](sections/07-self-explaining-code.md) |
| Mixing business rules with I/O, time, or mutation | [08](sections/08-functional-by-default.md) |
| Working on a large function, or laying out a feature's modules | [09](sections/09-decomposition-and-composition.md) |
| Deciding what to export, or what a function should accept | [10](sections/10-encapsulation-and-interfaces.md) |
| Looking at duplication, or adding an option to shared code | [11](sections/11-dry-and-wet.md) |
| Reaching for a design pattern, a compact one-liner, or a workaround | [12](sections/12-minimalism-and-elegance.md) |
| Adding a defensive check, a parameter, or "just in case" code | [13](sections/13-earned-complexity.md) |
| Deciding how a failure is represented and handled | [14](sections/14-errors-as-domain.md) |
| Writing tests, or finding code hard to test | [15](sections/15-testability.md) |
| Finding that a small change requires edits across many files | [16](sections/16-adaptability.md) |
| Tempted to disable a check or skip a layer | [17](sections/17-constraints-no-shortcuts.md) |

## How the section files are organised

Most section files follow the same structure:

- **Principle:** what the guideline is.
- **Why it matters:** the reasoning behind it.
- **Avoid:** an example of what not to do, and the problems it creates.
- **Prefer:** an example of what to do instead, what it resolves, and the benefits it brings.
