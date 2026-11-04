# 14. Errors Are Part of the Domain

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

> **Default stance, to be confirmed per codebase.** Follow the codebase's existing error-handling convention. If there is none, or this stance would conflict with it, raise it with the user ([Section 3](03-trade-offs-and-decision-log.md)).

## Principle

**Expected failures are domain outcomes**, and should be modelled as values in the type system (e.g. a `Result` type with named error variants). **Unexpected failures** (bugs, invariant violations, infrastructure crashes) may throw.

## Why it matters

- A thrown exception is invisible in a function's signature. Callers don't know what can fail or how.
- Named error variants tell the story of what can go wrong, in domain language, and can be matched exhaustively ([Section 6](06-discrete-states.md)).

## Avoid

```ts
function redeemVoucher(code: string): Voucher {
  const voucher = vouchers.get(code);
  if (!voucher) throw new Error("not found");
  if (voucher.expiresAt < new Date()) throw new Error("expired");
  if (voucher.redeemed) throw new Error("already used");
  return { ...voucher, redeemed: true };
}
```

**Problems:** the signature claims it always returns a `Voucher`. Failures are stringly typed, so callers have to catch and parse messages to tell them apart.

## Prefer

```ts
type RedemptionError =
  | { kind: "voucherNotFound"; code: VoucherCode }
  | { kind: "voucherExpired"; expiredAt: Date }
  | { kind: "voucherAlreadyRedeemed"; redeemedAt: Date };

const redeemVoucher = (voucher: Voucher | undefined, code: VoucherCode, now: Date): Result<RedeemedVoucher, RedemptionError> => {
  if (!voucher) return err({ kind: "voucherNotFound", code });

  switch (voucher.status) {
    case "redeemed": return err({ kind: "voucherAlreadyRedeemed", redeemedAt: voucher.redeemedAt });
    case "issued":
      return now > voucher.expiresAt
        ? err({ kind: "voucherExpired", expiredAt: voucher.expiresAt })
        : ok({ ...voucher, status: "redeemed", redeemedAt: now });
  }
};
```

**Benefits:** every failure mode is visible in the signature, named in domain language, and matchable exhaustively by callers. It's pure and testable without try/catch.
