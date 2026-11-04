# 19. Self-Review Checklist

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

Before presenting code as complete, confirm:

**Context and consistency**
- [ ] Follows the codebase's established patterns. Any divergence was raised and agreed.
- [ ] Respects framework and library idioms.
- [ ] Aligns with what the codebase optimises for.
- [ ] Does not replicate known anti-patterns. Any discovered anti-patterns or bugs were raised.
- [ ] No constraint was bypassed. Any challenge was raised with the user.

**Domain modelling**
- [ ] Names express domain concepts, not technical jargon.
- [ ] Types model the domain and tell its story. Illegal states are unrepresentable.
- [ ] Discrete states are named variants. Logic matches on them exhaustively.
- [ ] Untrusted input is parsed into domain types at the boundary.
- [ ] Expected failures are modelled as values, or follow the agreed codebase convention.

**Readability**
- [ ] Code reads like prose. Complex expressions are broken into well-named constants.
- [ ] Declarative where possible.
- [ ] Comments explain *why*, not *what*.

**Structure**
- [ ] Decomposition mirrors the domain. Pieces are understandable in isolation and compose clearly.
- [ ] Interfaces are minimal. Internals are hidden.
- [ ] Sharing reflects shared knowledge and reasons to change, not mere similarity.

**Simplicity**
- [ ] Every line, branch, parameter, and abstraction is justified.
- [ ] No impossible or defensive branches that the types already rule out.
- [ ] No workarounds and no over-engineering.
- [ ] Not clever or cryptic. Concise *and* clear.
- [ ] The solution could be explained quickly, and it feels like common sense.

**Functional design**
- [ ] Core logic is pure. Side effects sit at the edges.
- [ ] Inputs are not mutated.

**Testability and change**
- [ ] Easily testable in isolation, through a small public interface.
- [ ] Tests assert behaviour, not implementation, so a refactor won't break them.
- [ ] A likely future change would touch few places.

**Decisions**
- [ ] Trade-offs were raised with options and a recommendation. The user decided.
- [ ] Decisions are recorded in the decision log.
