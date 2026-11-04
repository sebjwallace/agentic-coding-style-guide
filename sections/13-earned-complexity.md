# 13. Complexity Must Be Earned

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Every line, branch, parameter, abstraction, and dependency must **justify its existence**. Question every logical branch: *does the domain actually require this, or is it accidental?*

## Why it matters

- Each branch doubles the paths a reader has to consider and a test has to cover.
- Defensive branches for situations the types already rule out are noise. They suggest uncertainty that shouldn't exist.
- Unused parameters, speculative options, and "just in case" code accumulate into a system nobody can simplify.

## Avoid

```ts
function shipOrder(order: PaidOrder): ShippedOrder {
  if (!order) throw new Error("No order");
  if (order.status !== "paid") throw new Error("Order not paid");
  if (!order.lines || order.lines.length === 0) throw new Error("No lines");
  // ...
}
```

**Problems:** the type already guarantees a paid order exists. The first two checks are impossible branches. If empty orders are genuinely invalid, that belongs in the type (e.g. a `NonEmptyArray<OrderLine>`), not a runtime check repeated in every function.

## Prefer

```ts
type PaidOrder = { status: "paid"; id: OrderId; lines: NonEmptyArray<OrderLine>; paidAt: Date };

function shipOrder(order: PaidOrder, tracking: TrackingNumber, now: Date): ShippedOrder {
  return { ...order, status: "shipped", shippedAt: now, trackingNumber: tracking };
}
```

**Benefits:** the rules live in the types. The function contains only essential logic, with no impossible branches to read, test, or maintain.

## Questions to ask of every branch

- Can this case actually happen, given the types?
- If it can, should the type be changed so it can't?
- Is this branch a domain rule (essential) or a symptom of a modelling gap (accidental)?
- Would naming the situation as a state ([Section 6](06-discrete-states.md)) remove the branch?
