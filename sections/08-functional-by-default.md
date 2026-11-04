# 8. Functional by Default

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Write **functional code as much as possible**:

- **Pure functions**: the same input always gives the same output, with no hidden dependencies and no side effects.
- **Declarative** style.
- **Side effects at a minimum**, and pushed to the edges of the system ("functional core, imperative shell").
- **Immutability**: don't mutate inputs or shared state; return new values.
- **State is representable in the domain language** (see [Sections 5](05-types-as-design-tool.md) and [6](06-discrete-states.md)).

If the codebase follows a different paradigm, consistency matters. Raise the trade-off rather than silently imposing a different style (see [Section 3](03-trade-offs-and-decision-log.md)).

## Why it matters

- Pure functions can be understood in isolation. Everything they depend on is in their signature.
- They are trivially testable: input in, output out, no mocks.
- Side effects (I/O, time, randomness, mutation) are where bugs, coupling, and test difficulty concentrate. Isolating them makes the rest of the code simple.

## 8.1 Functional core, imperative shell

### Avoid

```ts
async function renewSubscription(subscriptionId: string) {
  const subscription = await db.subscriptions.find(subscriptionId);
  if (new Date() > subscription.endsAt) {
    subscription.status = "expired";
    await db.subscriptions.save(subscription);
    await emailService.send(subscription.ownerEmail, "Your subscription expired");
    return;
  }
  subscription.endsAt = addMonths(subscription.endsAt, 1);
  await db.subscriptions.save(subscription);
}
```

**Problems:**
- The business rule (*when does a renewal succeed?*) is tangled with database access, the current time, mutation, and email.
- Testing the rule needs mocking the database, the email service, and the clock.
- The decision can't be reused or reasoned about independently.

### Prefer

```ts
// Functional core: pure domain logic
type RenewalOutcome =
  | { kind: "renewed"; subscription: ActiveSubscription }
  | { kind: "expired"; subscription: ExpiredSubscription };

const renew = (subscription: ActiveSubscription, now: Date): RenewalOutcome =>
  now > subscription.endsAt
    ? { kind: "expired", subscription: { ...subscription, status: "expired" } }
    : { kind: "renewed", subscription: { ...subscription, endsAt: addMonths(subscription.endsAt, 1) } };

// Imperative shell: effects at the edge
async function renewSubscription(id: SubscriptionId) {
  const subscription = await subscriptionRepository.findActive(id);
  const outcome = renew(subscription, clock.now());

  await subscriptionRepository.save(outcome.subscription);
  if (outcome.kind === "expired") await notifications.subscriptionExpired(outcome.subscription);
}
```

**Benefits:**
- The business rule is a small pure function, testable with plain values and no mocks.
- Time is an explicit input, so it's deterministic.
- The outcome is a named domain state, not an implicit early return.
- The shell is thin and obvious: load, decide, persist, notify.

## 8.2 Don't mutate inputs

### Avoid

```ts
function applyDiscount(cart: Cart, rate: number) {
  cart.items.forEach((item) => (item.price = item.price * (1 - rate)));
  return cart;
}
```

**Problems:** the caller's cart changes underneath them, which causes action-at-a-distance bugs. Applying the function twice compounds the discount, and the signature hides the mutation.

### Prefer

```ts
const applyDiscount = (cart: Cart, rate: DiscountRate): Cart => ({
  ...cart,
  items: cart.items.map((item) => ({ ...item, price: discounted(item.price, rate) })),
});
```

**Benefits:** no hidden effects, the original stays valid, and the function is safe to reuse, compose, and test.

> Prefer `readonly` types and arrays (`readonly Item[]`, `Readonly<T>`) to make immutability enforced, not just intended.
