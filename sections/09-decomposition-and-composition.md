# 9. Decompose to Mirror the Domain, Compose to Build the Whole

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Break code down so that its **decomposition reflects the composition of the domain**, whether that's the domain of a feature, a subsystem, or anything else.

- Each abstraction is **encapsulated** and can be **understood in isolation**.
- The whole is understood through **how the pieces compose**.

## Why it matters

- When code structure mirrors domain structure, readers can find their way around the code using their understanding of the domain, and vice versa.
- Pieces understood in isolation keep cognitive load low. Composition then shows how they interconnect, without the reader holding everything at once.
- Changes to one domain concept are localised to one piece of code.

## Avoid

```ts
async function checkout(req: Request) {
  // 150 lines: parse body, validate items, look up stock, compute prices,
  // apply promotions, compute tax, compute shipping, charge card,
  // reserve stock, write order, send confirmation email...
}
```

**Problems:** one unit holds many domain concepts. Nothing can be understood, tested, or changed alone. A change to tax rules risks breaking payments.

## Prefer

```ts
const priceBasket = (basket: ValidatedBasket, promotions: Promotion[]): PricedBasket =>
  pipe(basket, applyPromotions(promotions), addTax, addShipping);

async function checkout(request: CheckoutRequest): Promise<Result<PlacedOrder, CheckoutError>> {
  const basket = validateBasket(request.basket, await stock.levelsFor(request.basket));
  if (!basket.ok) return basket;

  const pricedBasket = priceBasket(basket.value, await promotions.active());
  const payment = await payments.charge(request.paymentMethod, pricedBasket.total);
  if (!payment.ok) return payment;

  return orders.place(pricedBasket, payment.value);
}
```

**Benefits:**
- Each step (validation, promotions, tax, shipping, payment, placement) is a named domain concept with its own unit.
- `priceBasket` reads as a description of how a basket is priced.
- `checkout` reads as the checkout process itself: the composition tells the story.
- Each step can be understood and tested in isolation.
