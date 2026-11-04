# 5. The Type System Is a Design Tool

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

The type system is a **first-class design tool**. Do not use it merely to pin down a structure or validate a shape. Use it to **model the domain**, so that the types clearly and directly communicate the domain's concepts and how they connect.

> Inspiration: *Domain Modeling Made Functional* (Scott Wlaschin). It shows how types can express a domain so purely that the model itself communicates the business.

## Why it matters

- When the types are right, **everything else falls into place**. Logic gets simpler, branches disappear, and invalid states can't be expressed.
- Types are a huge part of readability. **The types tell a story.** They paint a picture of the domain. Reading the type definitions of a module should tell you most of what you need to know about it.
- The compiler then enforces the domain's rules for you.

## 5.1 Make illegal states unrepresentable

### Avoid

```ts
type Order = {
  id: string;
  status: string;
  paidAt?: Date;
  shippedAt?: Date;
  trackingNumber?: string;
  cancellationReason?: string;
};
```

**Problems:**
- `status` can be any string, including typos.
- Nothing stops a cancelled order from having a `trackingNumber`, or a shipped order from lacking `paidAt`.
- Every consumer has to defensively check combinations of optional fields, which spreads accidental complexity everywhere.
- The type tells you nothing about the order lifecycle.

### Prefer

```ts
type OrderId = string & { readonly __brand: "OrderId" };

type UnpaidOrder = { status: "unpaid"; id: OrderId; lines: OrderLine[] };
type PaidOrder = { status: "paid"; id: OrderId; lines: OrderLine[]; paidAt: Date };
type ShippedOrder = {
  status: "shipped";
  id: OrderId;
  lines: OrderLine[];
  paidAt: Date;
  shippedAt: Date;
  trackingNumber: TrackingNumber;
};
type CancelledOrder = {
  status: "cancelled";
  id: OrderId;
  lines: OrderLine[];
  cancellationReason: CancellationReason;
};

type Order = UnpaidOrder | PaidOrder | ShippedOrder | CancelledOrder;
```

**Benefits:**
- The type tells the story of the order lifecycle: unpaid, paid, shipped, or cancelled.
- Invalid combinations cannot be constructed.
- A shipped order *always* has a tracking number, so there is no defensive checking.
- Functions can declare precisely what they accept: `shipOrder(order: PaidOrder): ShippedOrder`. That signature alone encodes a business rule (only paid orders ship).

## 5.2 Give domain concepts their own types

### Avoid

```ts
function transferFunds(from: string, to: string, amount: number) { /* ... */ }

transferFunds(amount, toAccount, fromAccount); // compiles, wrong
```

**Problems:** `string` and `number` carry no domain meaning. Arguments can be swapped silently, and `amount` could be negative, in the wrong currency, or in pence vs pounds.

### Prefer

```ts
type AccountId = string & { readonly __brand: "AccountId" };
type Money = { readonly amountInMinorUnits: number; readonly currency: Currency };

function transferFunds(from: AccountId, to: AccountId, amount: Money) { /* ... */ }
```

**Benefits:** the signature now speaks the domain. Swapping an account for an amount fails to compile, and units are explicit.

## 5.3 Parse at the boundary, don't validate everywhere

### Principle

Untrusted data (API responses, user input, files, environment) enters the system at the boundaries. **Parse it once, at the boundary, into domain types.** Inside the system, trust the types.

### Avoid

```ts
function sendWelcomeEmail(user: any) {
  if (!user.email || !user.email.includes("@")) throw new Error("bad email");
  // ...
}

function addToMailingList(user: any) {
  if (!user.email || !user.email.includes("@")) throw new Error("bad email");
  // ...
}
```

**Problems:** validation is duplicated and scattered, every function is defensive, and nothing guarantees anyone checked.

### Prefer

```ts
type EmailAddress = string & { readonly __brand: "EmailAddress" };

const parseEmailAddress = (raw: string): Result<EmailAddress, InvalidEmail> =>
  raw.includes("@") ? ok(raw as EmailAddress) : err({ kind: "invalidEmail", raw });

function sendWelcomeEmail(email: EmailAddress) { /* no checks needed */ }
function addToMailingList(email: EmailAddress) { /* no checks needed */ }
```

**Benefits:** validation happens once. Having an `EmailAddress` *is* proof that it was validated, and internal functions are free of defensive noise.
