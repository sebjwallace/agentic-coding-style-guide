# Agentic Coding Style Guide

A coding style guide written for AI coding agents working in TypeScript/JavaScript codebases. The principles are general; the examples are TypeScript.

It describes the target shape of the code an agent produces: code that models its domain, carries little accidental complexity, and stays consistent with the codebase around it. It also sets out how an agent should work towards that shape, including when to stop and ask the user to decide.

**If you are an agent, read [GUIDE.md](GUIDE.md) in full before you start, then follow the reading instructions below.**

## Why it is split up

The full guide is too long to keep in context for every task, and most of it is irrelevant at any given moment. It is arranged in two layers:

| Layer | File | Holds | Read it |
|---|---|---|---|
| Overview | [GUIDE.md](GUIDE.md) | Every principle in brief, and a map of what to read when | Once, in full, at the start of a task |
| Detail | [sections/](sections/) | One principle per file, with reasoning and worked examples | Only when relevant to the current work |

## How to read it

1. **Start with `GUIDE.md`.** It gives you the whole picture, so you can recognise when a principle applies even if you haven't opened its section.
2. **Stop there until the work calls for more.** Don't read the sections up front or in order. They are numbered for reference, not as a reading sequence.
3. **Use the reading map.** The "What to read next" part of `GUIDE.md` lists the sections to read for each stage of the work and for common situations. Before starting a stage, open the sections it lists that apply to the code in hand.
4. **Read one section at a time, when you need it.** Read it just before doing the work it covers, because that is when its examples are useful.
5. **Follow cross-references only if you need them.** Sections link to each other, but they make sense alone. Follow a link only if the linked topic affects what you are doing.
6. **Don't reread what you still have in context.** Reopen a section only if it has dropped out of your context and you need it again.

### Reading less

- **If you only need the rule,** read the start of a section file. Most open with the principle and why it matters; the examples come after. Read on when you are applying it to real code.
- **If you need one topic from a long section,** go to its subsection. [Section 5](sections/05-types-as-design-tool.md), for example, splits into 5.1 to 5.3.
- **If the task is small,** the overview and the [self-review checklist](sections/19-self-review-checklist.md) may be all you need. Add a section only if the change touches its topic.

### When to read more

Open the relevant section instead of relying on the overview when:

- The overview's summary doesn't settle how to apply a principle to your code.
- Principles seem to conflict. Read [Section 3](sections/03-trade-offs-and-decision-log.md), then raise the conflict with the user.
- You are about to diverge from a principle. Read its section first, to check that the divergence is justified.

## Layout

```
README.md     What this guide is, and how to read it
GUIDE.md      The overview: start here
sections/     One file per principle, numbered to match GUIDE.md
```

## License

See [LICENSE](LICENSE).
