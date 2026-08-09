# Context Kit

Prompts, patterns, and tooling to reduce LLM context and token usage in coding workflows.

The goal is to make coding agents spend less time rediscovering the repository and less context on information that can be compressed or persisted.

## Core files

This repository currently focuses on generating and maintaining:

* `.ai/architecture.md` — how the system works
* `.ai/conventions.md` — how code should be written in this repository
* `.ai/current-state.md` — what is happening in the project right now
* `AGENTS.md` — tells coding agents when to load each context file

The `.ai/` files should contain compressed, high-signal project knowledge rather than exhaustive documentation.

## Recommended project structure

```text
AGENTS.md

.ai/
  architecture.md
  conventions.md
  current-state.md
```

## Updating `AGENTS.md`

Add a context-loading section to your project's `AGENTS.md`:

```md
## Context loading

Load project context selectively. Do not read all project documentation by default.

- `.ai/architecture.md`
  Read for architectural, cross-cutting, backend, data-flow, integration, or unfamiliar feature work.

- `.ai/conventions.md`
  Read before implementing non-trivial features or refactoring code where repository-specific patterns matter.

- `.ai/current-state.md`
  Read when working on active or recently changed features, or when recent project work may affect the task.

Prefer targeted code search after loading the relevant context.

Do not re-discover information already documented in these files unless:
- the documentation conflicts with the code,
- the relevant implementation has changed,
- or the documentation is insufficient for the current task.

Do not load these files for trivial or isolated changes when they are not relevant.
```

The important part is **selective loading**.

Avoid instructions such as:

```md
Always read architecture.md, conventions.md and current-state.md before every task.
```

That turns persistent context into a fixed token cost on every prompt.

Instead, `AGENTS.md` should act as a router that tells the agent which context is relevant to the task.

## Generating the context files

Use the prompts in this repository against the target codebase.

Recommended cadence:

* `architecture.md`: generate initially and refresh after meaningful architectural changes.
* `conventions.md`: generate initially and refresh when development patterns materially change.
* `current-state.md`: regenerate frequently as active work changes.

These files should be treated as compressed machine-facing context, not traditional project documentation.

## Principle

The coding prompt should mostly describe **what needs to be done**.

The repository should already provide enough compressed context for the agent to determine **how this project works and how changes should be implemented** without repeatedly exploring the entire codebase.

