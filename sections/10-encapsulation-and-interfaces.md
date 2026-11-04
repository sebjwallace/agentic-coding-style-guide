# 10. Encapsulation and Minimal Interfaces

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Functions and modules **only reveal as much as they need to**. Interfaces are **minimal**. Implementation details stay hidden behind a small, meaningful surface.

## Why it matters

- Every exported function, field, or parameter is a promise and a coupling point. The more exposed, the more that can depend on internals, and the harder change becomes.
- A small interface is easier to understand, easier to test through, and easier to keep stable while the implementation evolves.

## Avoid

```ts
// pricing.ts
export const BASE_RATES = { /* ... */ };
export function lookupRate(sku: string) { /* ... */ }
export function roundToPence(value: number) { /* ... */ }
export function applyBulkTier(qty: number, rate: number) { /* ... */ }
export function priceLine(line: OrderLine) { /* uses all of the above */ }
```

**Problems:** callers and tests start depending on `lookupRate`, `applyBulkTier`, and `BASE_RATES`. Restructuring pricing internals now breaks unrelated code. The module's real purpose is hidden among its parts.

## Prefer

```ts
// pricing.ts
const baseRates = { /* ... */ };
const lookupRate = (sku: Sku) => { /* ... */ };
const applyBulkTier = (quantity: Quantity, rate: Money) => { /* ... */ };

export const priceLine = (line: OrderLine): PricedLine => { /* ... */ };
```

**Benefits:** one exported concept, `priceLine`. Internals can be restructured freely, and the module's purpose is obvious from its interface.

> The same applies to function parameters: accept only what the function needs (`(email: EmailAddress)`, not `(user: User)` when only the email is used).
