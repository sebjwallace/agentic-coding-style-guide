# 1. Core Philosophy

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Code is a **model** of the thing it represents. Its job is to express the **essential complexity** of the problem and to remove as much **accidental complexity** as possible.

The world, its data, and most code are full of noise, and noise creates problems. This guide is about **reducing noise**: finding the elegant, simple, principled solution, while staying consistent with the codebase you are working in.

## Why it matters

- **Essential complexity** belongs to the problem itself. The domain really does have these rules, states, and relationships.
- **Accidental complexity** comes from how we chose to solve it: workarounds, needless indirection, defensive branches for impossible cases, technical plumbing leaking into domain logic.
- Every unnecessary moving part has to be read, understood, tested, and maintained, and it can break. Fewer parts make code cheaper and quicker to change.
- **Simplicity is hard to achieve.** The first working solution is rarely the simplest. Finding the simple one takes deliberate work, and that work pays off every time the code is read or changed afterwards.

## The standard

- Every line of code must be **justified**. A line added purely for readability is justified, because it communicates something of value.
- Every logical branch should be **questioned**.
- Complexity must be **earned** (see [Section 13](13-earned-complexity.md)).
- A good solution should **feel like common sense**. If you explained it to someone, it should not take long. If a solution needs a long explanation, look for a simpler one.
- Code should **read eloquently**.

These principles serve one another:

| Quality | Supports |
|---|---|
| Domain-driven types | Readability, correctness, fewer branches |
| Readability | Changeability, reviewability |
| Purity and decoupling | Testability, changeability |
| Minimalism | Everything: less to read, test, and break |
| Consistency | Predictability: no surprises for people who know the codebase |
