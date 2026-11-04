# 6. Model Discrete States, Then Match on Them

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Represent the **discrete states and values** of the domain explicitly, as named variants (discriminated unions of string literals or tagged objects). Then express logic by **matching on those states**, exhaustively, rather than through tangles of `if`/`&&`/`||` conditions.

## Why it matters

- Logic written as nested booleans forces the reader to evaluate combinations in their head to work out what situation the code is in.
- Named states turn implicit situations into **explicit domain concepts**. That is a large part of readability, and it goes back to using the type system as a modelling tool.
- Exhaustive matching means that when a new state is added, the compiler tells you every place that needs to handle it.

## 6.1 Replace boolean combinations with named states

### Avoid

```ts
type RequestState = {
  isLoading: boolean;
  isError: boolean;
  error?: string;
  data?: Product[];
};

if (state.isLoading && !state.isError) { /* ... */ }
else if (!state.isLoading && state.isError && state.error) { /* ... */ }
else if (!state.isLoading && !state.isError && state.data) { /* ... */ }
```

**Problems:**
- Four booleans and optionals allow 16+ combinations, most of them meaningless (`isLoading && isError`?).
- The reader has to decode which combination means which situation.
- It's easy to miss a case, and nothing warns you.

### Prefer

```ts
type ProductsRequest =
  | { status: "notRequested" }
  | { status: "loading" }
  | { status: "failed"; error: FetchError }
  | { status: "loaded"; products: Product[] };

function renderProducts(request: ProductsRequest) {
  switch (request.status) {
    case "notRequested": return <Prompt />;
    case "loading":      return <Spinner />;
    case "failed":       return <ErrorMessage error={request.error} />;
    case "loaded":       return <ProductList products={request.products} />;
    default:             return assertNever(request);
  }
}

function assertNever(value: never): never {
  throw new Error(`Unhandled variant: ${JSON.stringify(value)}`);
}
```

**Benefits:**
- Exactly four states, each named, and no impossible combinations.
- Each branch reads as a sentence: *when loading, show a spinner*.
- Data is only available in the state where it exists.
- The `assertNever` default makes the match **exhaustive**. Add a variant and the compiler flags every unhandled `switch`.

## 6.2 Classify into named states first, then act

### Avoid

```ts
function shippingCost(order: Order, customer: Customer): number {
  if (customer.tier === "gold" || (order.total > 50 && order.region === "UK")) {
    return 0;
  } else if (order.region !== "UK" && order.weightKg > 20) {
    return 40;
  } else if (order.region !== "UK") {
    return 15;
  }
  return 4.99;
}
```

**Problems:** the business concepts are buried in conditions. What *is* "gold or over £50 in the UK"? The reader has to infer that it means "free shipping". The rules are hard to verify and fragile to change.

### Prefer

```ts
type ShippingBand = "free" | "domesticStandard" | "international" | "internationalHeavy";

function classifyShipping(order: Order, customer: Customer): ShippingBand {
  const qualifiesForFreeShipping =
    customer.tier === "gold" || (order.region === "UK" && order.total > FREE_SHIPPING_THRESHOLD);
  const isInternational = order.region !== "UK";
  const isHeavy = order.weightKg > HEAVY_PARCEL_KG;

  if (qualifiesForFreeShipping) return "free";
  if (isInternational) return isHeavy ? "internationalHeavy" : "international";
  return "domesticStandard";
}

const shippingCostByBand: Record<ShippingBand, Money> = {
  free: gbp(0),
  domesticStandard: gbp(4.99),
  international: gbp(15),
  internationalHeavy: gbp(40),
};

const shippingCost = (order: Order, customer: Customer): Money =>
  shippingCostByBand[classifyShipping(order, customer)];
```

**Benefits:**
- The domain concepts are named: shipping bands, free-shipping qualification, heavy parcels.
- Classification (*which situation are we in?*) is separate from consequence (*what does it cost?*). Each can be read, tested, and changed independently.
- `Record<ShippingBand, Money>` is exhaustive by construction: add a band and the compiler demands a price.

## 6.3 Replace boolean parameters with named options

### Avoid

```ts
exportReport(report, true, false);
```

**Problems:** at the call site, `true, false` means nothing.

### Prefer

```ts
exportReport(report, { format: "pdf", audience: "internal" });
```

**Benefits:** the call site explains itself, and adding a third format doesn't need another boolean.
