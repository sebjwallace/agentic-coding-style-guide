# 3. Trade-offs and the Decision Log

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

When principles in this guide conflict, or when a decision has meaningful trade-offs, **do not decide silently**. Instead:

1. **Raise it with the user.**
2. **Present the options**, each with its trade-offs.
3. **Give a recommendation**, with your reasoning.
4. **The user makes the decision.**
5. **Record the decision** in the project's decision log.

The same applies to **challenging a constraint**: you may believe a constraint is no longer needed, and you may be right. But the challenge must go to the user. Do not quietly work around it.

## Why it matters

- Principles conflict in practice: consistency vs a better pattern, minimalism vs readability, DRY vs WET, framework idiom vs codebase need.
- The user holds context you don't: history, organisational constraints, future plans.
- A decision log stops the same debate from recurring, explains *why* the code looks the way it does, and lets future agents and humans align with past decisions.

## Common trade-offs to raise

- The codebase's established pattern conflicts with this guide (e.g. a mutable, class-based codebase vs the functional preference in [Section 8](08-functional-by-default.md)).
- A framework's idiom conflicts with the codebase's needs.
- Sharing code would reduce duplication but couple things that change for different reasons.
- A constraint seems unnecessary or counterproductive.
- An anti-pattern or bug was discovered outside the current task's scope.

## Example

### Avoid

Silently introducing a `Result` type into a codebase that throws exceptions everywhere, because this guide prefers errors as values.

**Problems:** the codebase now has two error models, the user didn't choose this, and nobody knows why the new code differs.

### Prefer

> **Trade-off: error handling in the new payments module**
>
> The codebase throws exceptions for all failures. This guide prefers modelling expected failures as values.
>
> - **Option A: Follow existing convention (throw).** Consistent with the codebase. Failures stay invisible in function signatures.
> - **Option B: Use `Result` in the new module only.** Explicit failure modes in this module. Introduces a second error model, which needs a boundary adapter.
> - **Option C: Adopt `Result` and migrate incrementally.** Long-term consistency with explicit errors. Largest scope.
>
> **Recommendation:** A for now, with C logged as a candidate follow-up. Consistency within the codebase outweighs local improvement here.
>
> Which would you like?

Once the user decides, record it (see the [Appendix](appendix-decision-log-template.md)).
