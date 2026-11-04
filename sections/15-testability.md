# 15. Testability

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Code must be **easily testable in isolation**. That requires decoupling and good boundaries. **If code is easy to test, it is usually well designed; if it is hard to test, the design is telling you something.**

Tests should exercise behaviour **through a small, public interface**, not internal details.

## Why it matters

- Testable code is decoupled code: pure functions, explicit dependencies, effects at the edges ([Section 8](08-functional-by-default.md)).
- Tests tightly coupled to implementation break whenever the code is refactored, even when behaviour is unchanged. That punishes improvement and erodes trust in the tests.
- Testing through public interfaces lets the implementation evolve freely.

## Avoid

```ts
it("prices a line", () => {
  const lookupSpy = vi.spyOn(internals, "lookupRate").mockReturnValue(10);
  const tierSpy = vi.spyOn(internals, "applyBulkTier");

  priceLine(line);

  expect(lookupSpy).toHaveBeenCalledWith("SKU-1");
  expect(tierSpy).toHaveBeenCalledTimes(1);
});
```

**Problems:** the test asserts *how* pricing works internally, not *what* it produces. Renaming or restructuring internals breaks the test even when prices are unchanged. It needs internals to be exported, which violates [Section 10](10-encapsulation-and-interfaces.md).

## Prefer

```ts
it("applies the bulk tier discount for 100 or more units", () => {
  const bulkLine = orderLine({ sku: widgetSku, quantity: 100 });

  expect(priceLine(bulkLine).total).toEqual(gbp(900));
});
```

**Benefits:**
- Tests behaviour through the public interface, in domain language.
- The test name states a business rule, and the test documents it.
- Internals can be refactored freely, and the test only fails when behaviour actually changes.
- No mocks are needed, because the function is pure.

## Guidelines

- Prefer pure functions so most tests are plain input/output.
- Mock only at true system boundaries (network, database, clock), and prefer passing dependencies in over module-level mocking.
- Name tests after the behaviour or rule they verify.
- One concept per test, so that failures point directly at what broke.
