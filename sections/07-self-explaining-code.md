# 7. Code That Explains Itself

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

The best code **explains itself**. Reading it should feel like reading natural language. The reader should understand what the code is doing effortlessly and seamlessly, because the code is **offering itself to the reader**.

To achieve this:

- Write **declaratively** as much as possible: say *what*, not *how*.
- Use **descriptive names** (see [Section 4](04-domain-language.md)).
- **Break logic into named chunks.** Assign meaningful intermediate values to well-named constants, so that the final composition **reads like a sentence**.

## Why it matters

- Logic bunched onto a single line, with transformations, conditions, and calls chained together, forces the reader to chunk it in their own head and give each piece meaning themselves.
- When the code has *already* chunked it, with each chunk named for the concept it represents, the reader is handed the meaning directly.
- The names explain the concepts. The final expression becomes a summary of the logic in domain language.
- This is one place where **more lines are justified**: each added line carries meaning (see [Section 12](12-minimalism-and-elegance.md)).

## 7.1 Name the chunks

### Avoid

```ts
const result = users
  .filter((u) => u.lastLogin > Date.now() - 30 * 24 * 60 * 60 * 1000 && u.plan !== "free")
  .reduce((sum, u) => sum + u.seats * (u.plan === "team" ? 12 : 25), 0) * (1 - (region === "EU" ? 0.1 : 0));
```

**Problems:** a reader must parse a date calculation, a plan filter, a per-plan price lookup, a reduction, and a regional discount, all at once, with no names to guide them. What is `result`?

### Prefer

```ts
const activeWindowStart = daysAgo(ACTIVE_USER_WINDOW_DAYS);
const isRecentlyActive = (user: User) => user.lastLogin > activeWindowStart;
const isPaying = (user: User) => user.plan !== "free";

const activePayingUsers = users.filter((user) => isRecentlyActive(user) && isPaying(user));
const grossMonthlyRevenue = sum(activePayingUsers.map(monthlyRevenueFor));
const regionalDiscount = discountRateFor(region);

const projectedMonthlyRevenue = grossMonthlyRevenue * (1 - regionalDiscount);
```

**Benefits:**
- Each constant names a concept: active window, recently active, paying, gross revenue, regional discount.
- The final line reads as a sentence: *projected monthly revenue is gross monthly revenue less the regional discount*.
- Each piece can be understood, tested, and changed in isolation.

## 7.2 Declarative over imperative

### Avoid

```ts
const names: string[] = [];
for (let i = 0; i < employees.length; i++) {
  if (employees[i].department === "engineering") {
    names.push(employees[i].firstName + " " + employees[i].lastName);
  }
}
names.sort();
```

**Problems:** the reader tracks an index, a mutable accumulator, and a branch, then a mutation after the fact, all to work out the intent.

### Prefer

```ts
const engineers = employees.filter(isInDepartment("engineering"));
const engineerNames = engineers.map(fullName).toSorted();
```

**Benefits:** states *what* is wanted (engineers' names, sorted) rather than *how* to assemble it. There's no mutable state, and the intent is immediate.

> Declarative doesn't mean "chain everything into one expression". If a chain gets long, break it into named steps (7.1).

## 7.3 Comments explain *why*, not *what*

If the code needs a comment to explain *what* it does, improve the names and structure instead. Reserve comments for *why*: the non-obvious reason, the external constraint, the decision that was taken (link to the decision log where relevant).

### Avoid

```ts
// loop over orders and add up totals where status is paid
let t = 0; for (const o of orders) if (o.s === "paid") t += o.v;
```

### Prefer

```ts
const paidOrders = orders.filter(isPaid);
// Refunds are excluded here: finance reconciles them separately (see DECISIONS.md #14).
const paidRevenue = sum(paidOrders.map(orderTotal));
```
