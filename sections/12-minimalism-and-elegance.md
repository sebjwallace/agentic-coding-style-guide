# 12. Minimalism and Elegance

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

**Do as much as you can with as little code as possible**, without becoming **too clever or too cryptic**. Do more with less, but not to the point where you sacrifice the other principles in this guide (readability, domain clarity, consistency).

Solutions should be **elegant**: they solve problems **without workarounds** and **without over-engineering**. Seek the **simplest solution**. It often takes more work to find, and it is worth it.

## Why it matters

- Less code means fewer moving parts, less overhead, less noise, less to test, and less to break. Code with fewer parts is cheaper and quicker to change.
- Clever, compressed code saves characters but costs comprehension. Line count isn't the measure; the effort to understand is.
- Over-engineering (speculative abstraction, needless indirection, patterns for their own sake) is accidental complexity.
- Workarounds hide the real problem and add complexity that compounds.

## 12.1 Over-engineering

### Avoid

```ts
interface NotificationStrategy { send(message: Message): Promise<void> }
class EmailNotificationStrategy implements NotificationStrategy { /* ... */ }
class SmsNotificationStrategy implements NotificationStrategy { /* ... */ }
class NotificationStrategyFactory {
  create(channel: string): NotificationStrategy { /* ... */ }
}
class NotificationService {
  constructor(private factory: NotificationStrategyFactory) {}
  notify(channel: string, message: Message) { return this.factory.create(channel).send(message); }
}
```

**Problems:** four types and a factory to choose between two functions. That's indirection for anticipated flexibility nobody asked for.

### Prefer

```ts
type Channel = "email" | "sms";

const senders: Record<Channel, (message: Message) => Promise<void>> = {
  email: sendEmail,
  sms: sendSms,
};

const notify = (channel: Channel, message: Message) => senders[channel](message);
```

**Benefits:** the same capability in a fraction of the code. It's exhaustive over channels, and trivial to extend when a real third channel arrives.

## 12.2 Too clever

### Avoid

```ts
const g = (s: number) => ["F", "D", "C", "B", "A"][Math.min(4, Math.max(0, ~~(s / 10) - 5))];
```

**Problems:** short, but the reader has to decode a double bitwise NOT, an index offset, and clamping to recover a simple grading rule.

### Prefer

```ts
const gradeBoundaries: ReadonlyArray<[minimumScore: number, grade: Grade]> = [
  [90, "A"], [80, "B"], [70, "C"], [60, "D"],
];

const gradeFor = (score: Score): Grade =>
  gradeBoundaries.find(([minimumScore]) => score >= minimumScore)?.[1] ?? "F";
```

**Benefits:** still concise, and the grading rule is now visible as data. It reads like the policy it implements.

## 12.3 Workarounds

### Avoid

```ts
saveProfile(profile);
setTimeout(() => refreshProfileView(), 500); // wait for save to finish
```

**Problems:** guesses at timing instead of fixing ordering. It's flaky under load, and it hides a design problem.

### Prefer

```ts
const savedProfile = await saveProfile(profile);
refreshProfileView(savedProfile);
```

**Benefits:** expresses the real dependency (refresh after save, with the saved data). Deterministic, no magic numbers.
