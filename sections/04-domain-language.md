# 4. Speak the Language of the Domain

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Code should **speak the language of the domain and the problem space**, not technical jargon. Names should express **real concepts**. Naming is one of the main ways code explains itself to its reader.

## Why it matters

- Code is a model of something real. When its vocabulary matches that something, readers who understand the domain understand the code, and the code teaches the domain to those who don't.
- Generic technical names (`data`, `item`, `obj`, `handler`, `process`, `manager`, `flag`, `temp`) carry no meaning. The reader has to reconstruct what they represent.
- Domain names expose mismatches. When the code says `cancelShipment` but the business calls it "recall", you have found a modelling gap.

## Avoid

```ts
function processData(data: Item[], flag: boolean) {
  return data.filter((x) => x.s === 2 && (flag || x.d > Date.now()));
}
```

**Problems:**
- `processData`, `data`, `Item`, `flag`, `x`, `s`, `d` say nothing about the domain.
- `2` is a magic number, and the reader has to find out what status 2 means.
- The reader has to reverse-engineer the business rule from the mechanics.

## Prefer

```ts
function findRedeemableVouchers(vouchers: Voucher[], includeExpired: boolean): Voucher[] {
  return vouchers.filter(
    (voucher) => voucher.status === "issued" && (includeExpired || !hasExpired(voucher)),
  );
}
```

**Benefits:**
- The function name states the business intent: *redeemable vouchers*.
- Status is a named domain value, not a number.
- `hasExpired` names a domain concept and hides its mechanics.
- A product owner could read this line and confirm whether it's right.

> Even better: see [Section 6](06-discrete-states.md) for replacing the `includeExpired` boolean flag with an explicit, named choice.

## Naming guidelines

- Name things after **what they mean**, not how they're implemented (`activeSubscribers`, not `filteredUserArray`).
- Use the domain's own words consistently. Pick one term per concept and stick to it across code, types, tests, and UI.
- Functions are named for **what they achieve** (`calculateShippingCost`), not what they do mechanically (`loopAndSum`).
- Booleans read as questions or facts (`isEligible`, `hasExpired`).
- Avoid abbreviations unless they are the domain's own vocabulary (e.g. `VAT`, `SKU`).
