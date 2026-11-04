# Appendix: Decision Log Template

*Part of the [Coding Style Guide](../GUIDE.md). Read that overview first if you haven't.*

Keep a small, append-only decision log in the repository (e.g. `DECISIONS.md`), or use the project's existing format (ADRs, etc.) if one exists. Each entry is short:

```md
## #<number>: <short title>

- **Date:** YYYY-MM-DD
- **Status:** Accepted | Superseded by #<n> | Revisited
- **Context:** What prompted the decision, and which principles or constraints were in tension.
- **Options considered:**
  - **A: <option>** – trade-offs
  - **B: <option>** – trade-offs
- **Agent recommendation:** <option> – one-line reasoning
- **Decision:** <option chosen by the user>
- **Consequences:** What this means for future code; any follow-ups.
```

## Example entry

```md
## #7: Error handling in the payments module

- **Date:** 2026-11-04
- **Status:** Accepted
- **Context:** Codebase throws exceptions everywhere; the style guide prefers modelling expected failures as values.
- **Options considered:**
  - **A: Throw, as elsewhere** – consistent; failures invisible in signatures.
  - **B: Result type in payments only** – explicit failures; two error models.
  - **C: Adopt Result and migrate incrementally** – long-term consistency; largest scope.
- **Agent recommendation:** A, with C as a follow-up – consistency outweighs a local improvement.
- **Decision:** B – payments failures are business-critical and need to be explicit.
- **Consequences:** Payments exposes Result; an adapter converts to exceptions at the API boundary. Revisit C next quarter.
```
