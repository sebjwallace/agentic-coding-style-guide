# 2. Understand the Codebase Before Writing

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Before writing code, **discover the regularities** of the codebase you are working in, and align with them. Specifically, discover:

1. **Patterns**: how things are typically done here (data access, state management, error handling, module layout, naming, testing style).
2. **Constraints**, and **why they exist**.
3. **Principles**: the values the codebase holds (e.g. immutability, spec-first, strict typing).
4. **Optimisation goals**: what the code is trying to optimise for (latency, bundle size, readability, auditability, offline support, etc.).
5. **Framework and library idioms**: the standards and idiomatic usage of the tools in use.
6. **Anti-patterns and bugs**, so that you do not replicate them.

## Why it matters

- **Regularity means no surprises.** Anyone who has worked in the codebase already carries its patterns in their head. When your code repeats those patterns, they understand it immediately. That is consistency.
- Code that ignores local patterns, however "good" it is in isolation, adds a second way of doing things. Every additional way raises the cost of reading the codebase.
- Knowing *what the code optimises for* lets you make choices that align with those goals instead of accidentally working against them.
- Frameworks have conventions for good reasons. Fighting them produces fragile code that breaks when the framework evolves.

## Rules

- **Follow established patterns** unless there is a good reason not to. If there is, raise it with the user (see [Section 3](03-trade-offs-and-decision-log.md)).
- **Do not replicate anti-patterns.** Consistency means consistency with the codebase's *good* patterns. If you discover an anti-pattern or a bug, **raise it with the user**. Do not silently copy it, and do not silently "fix" it outside the scope of your task.
- **Respect framework idioms** while also considering the needs of the codebase itself. Where the two tension, raise it.

## Example: following an established pattern

The codebase accesses data through repository functions that return domain types:

```ts
// existing pattern in src/orders/orderRepository.ts
export const findOrderById = (id: OrderId): Promise<Result<Order, OrderNotFound>> => { /* ... */ };
```

### Avoid

```ts
// in a new feature component
const response = await fetch(`/api/orders/${id}`);
const order = await response.json(); // untyped, unparsed
if (!order) throw new Error("not found");
```

**Problems:**
- Introduces a second, competing way of loading orders.
- Bypasses parsing, so `order` is effectively `any`, and the domain type is lost.
- Error handling is inconsistent with the rest of the codebase (throwing vs `Result`).
- Anyone familiar with the codebase is surprised, and has to ask whether there is a reason this one is different.

### Prefer

```ts
const orderResult = await findOrderById(orderId);
```

**Benefits:**
- One way to load an order. Readers recognise it instantly.
- Domain types and error semantics are preserved.
- Changes to how orders are loaded happen in one place.

## Example: respecting framework idioms (React)

### Avoid

```tsx
const [fullName, setFullName] = useState("");

useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);
```

**Problems:**
- Duplicates state that can be derived, and causes an extra render.
- Opens the door to state falling out of sync.
- Goes against React's documented idiom: don't use effects to derive state.

### Prefer

```tsx
const fullName = `${firstName} ${lastName}`;
```

**Benefits:**
- No redundant state, no synchronisation, no extra render.
- Idiomatic React that any React developer will expect.

## Example: encountering an anti-pattern

You find that existing code routinely casts with `as any` to silence type errors.

### Avoid

Writing your new code with `as any` "to stay consistent".

### Prefer

Write your code properly typed, and **raise the pattern with the user**:

> "I noticed `as any` is used in several places in `src/billing/` to bypass type errors. This hides real mismatches. I've typed my changes properly. Would you like me to log this as a follow-up, or address it now?"
