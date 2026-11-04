# 16. Adaptability and Evolvability

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Code should be **easy to change**. This follows from everything above: minimal, simple, decoupled code with few interconnected parts can change **without breaking other things**. The same applies to tests.

## Why it matters

- Requirements will change. The cost of a codebase is mostly the cost of changing it.
- Fewer parts and fewer connections between them mean a change in one place stays in one place.

## Avoid

```ts
// many modules reach into the same shape directly
const isVip = customer.orders.length > 10 && customer.account.flags.includes("vip");
```

repeated, slightly differently, across ten files.

**Problems:** changing what "VIP" means, or the shape of `customer`, means finding and updating every copy. Each copy may already have drifted.

## Prefer

```ts
// customer.ts
export const customerTier = (customer: Customer): CustomerTier => { /* single definition */ };
```

**Benefits:** the concept has one home and one interface. Its definition and the underlying data shape can both change without touching callers.

## Signals of poor evolvability

- A small change requires edits across many files.
- Changing an implementation detail breaks many tests.
- Shared code has grown flags for each caller ([Section 11](11-dry-and-wet.md)).
- Modules know about each other's internals ([Section 10](10-encapsulation-and-interfaces.md)).
