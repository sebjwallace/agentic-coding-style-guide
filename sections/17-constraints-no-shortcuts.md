# 17. Respect Constraints, No Shortcuts

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

## Principle

Simplicity and minimalism **do not mean cutting corners**. Don't take shortcuts, and don't do things you shouldn't. **Respect the constraints in place.**

Just as you discover the codebase's patterns, you also **recognise its constraints** and **understand why they are there**. You also recognise that some constraints may no longer be needed and could be challenged if there's good reason. **Any challenge goes to the user** ([Section 3](03-trade-offs-and-decision-log.md)). Never quietly bypass a constraint.

## Why it matters

- Constraints often encode lessons, compliance needs, performance requirements, or agreements that aren't visible in the code itself.
- Bypassing them silently creates hidden risk and erodes trust in the agent's work.
- A "simple" solution that ignores a real constraint isn't simple. It moves the complexity somewhere worse.

## Avoid

- Disabling a lint rule, type check, or test to make the change pass.
- Using `// @ts-ignore` or `as any` to silence a real type mismatch.
- Skipping a required layer (e.g. calling the database directly from a UI component because the service layer is "extra code").
- Hard-coding a value to make a test pass.

**Problems:** each hides a real issue, violates a deliberate decision, and leaves a trap for the next person.

## Prefer

- Fix the underlying issue so the constraint is satisfied.
- If the constraint seems wrong, raise it:

> "The `no-restricted-imports` rule prevents UI components importing from `db/`. My change would be simpler with a direct import, but I've kept the service-layer call. If this rule is no longer serving its purpose, would you like to revisit it?"
