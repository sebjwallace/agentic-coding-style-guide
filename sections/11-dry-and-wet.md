# 11. Balancing DRY and WET

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

*Don't Repeat Yourself* is a good principle, but it can be taken too far. Over-shared code accumulates **contention and overload** until it becomes complex in its own right. **Sometimes things aren't meant to be bunched together; sometimes they're meant to be separated out.**

The test: **share code when it represents the same knowledge and changes for the same reason.** Keep code separate when it merely *looks* similar today but serves different concepts that will evolve independently.

## Why it matters

- Duplicated *knowledge* (a business rule, a calculation) drifts out of sync, which creates bugs.
- Prematurely shared *code* couples unrelated concepts. Every caller's special case becomes a flag or parameter in the shared function, and a change for one caller risks breaking another.

## 11.1 Over-DRY: shared code overloaded by unrelated callers

### Avoid

```ts
function formatAddress(
  address: Address,
  options: { singleLine?: boolean; includeCountry?: boolean; forLabel?: boolean; uppercasePostcode?: boolean; omitCompany?: boolean },
) {
  // 60 lines of branches serving the invoice, the shipping label, and the profile page
}
```

**Problems:** three different concepts (an invoice address block, a shipping label, a profile summary) are forced through one function. Every option interacts with the others, a change for labels can break invoices, and nobody can understand the function alone.

### Prefer

```ts
const invoiceAddressBlock = (address: Address): string[] => [ /* ... */ ];
const shippingLabelAddress = (address: Address): string[] => [ /* ... */ ];
const profileAddressSummary = (address: Address): string => /* ... */;
```

Shared genuinely common pieces stay shared, for example `formatPostcode(address.postcode)`.

**Benefits:** each format represents its own concept and can change independently. Each function is small and readable, and what's truly common is still shared.

## 11.2 Over-WET: duplicated knowledge

### Avoid

```ts
// in checkout.ts
const vat = subtotal * 0.2;
// in invoice.ts
const vat = net * 0.2;
// in reports.ts
const vatOwed = sales * 0.2;
```

**Problems:** one business rule (the VAT rate and how it's applied) lives in three places. When it changes, someone will miss one.

### Prefer

```ts
// tax.ts
export const vatOn = (amount: Money): Money => multiply(amount, STANDARD_VAT_RATE);
```

**Benefits:** one source of truth for a single piece of domain knowledge.
