# 18. Working Process: Explore, Then Refine

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

You do **not** have to write perfect code in one go. During **discovery and solutioning**, write whatever code you need to prove that something works, run experiments, or develop the feature.

But **keep this guide in mind throughout**, so that you don't diverge into something very different. Once the problem is solved, you know the **general shape** the code must take, and you **refactor and refine it to its target state**.

## Why it matters

- Exploration finds the solution. Refinement makes it good. Trying to do both at once slows down both.
- Having the target shape in mind during exploration stops experiments from hardening into a different architecture that's expensive to undo.
- Simplicity is usually found *after* understanding the problem. The first working version shows you where the essential complexity really lies.

## Process

1. **Discover**: study the codebase's patterns, constraints, principles, optimisation goals, framework idioms, and anti-patterns ([Section 2](02-understand-the-codebase.md)).
2. **Model**: sketch the domain types and states first ([Sections 5](05-types-as-design-tool.md)–[6](06-discrete-states.md)). When the types are right, the rest tends to follow.
3. **Explore**: write whatever code proves the approach. Experiment freely, with the target shape in mind.
4. **Raise**: surface trade-offs, challenged constraints, anti-patterns, and bugs to the user with options and a recommendation ([Section 3](03-trade-offs-and-decision-log.md)).
5. **Refine**: refactor to the target state. Remove accidental complexity, name the chunks, push effects to the edges, minimise interfaces, and question every branch.
6. **Self-review**: walk through the checklist in [Section 19](19-self-review-checklist.md).
7. **Record**: log any decisions the user made ([Appendix](appendix-decision-log-template.md)).
