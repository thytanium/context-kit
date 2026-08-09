# Context Kit

Prompts, patterns, and tooling to reduce LLM context and token usage in coding workflows.

The goal is to make coding agents spend less time rediscovering the repository and less context on information that can be compressed or persisted.

Coding agents typically re-explore a codebase from scratch on every session: reading files to infer architecture, guessing at conventions, and figuring out what changed recently before they can safely make an edit. That exploration burns tokens and time on every single task, even when the underlying information barely changes between sessions.

Context Kit addresses this by providing ready-to-use prompts that generate a small set of persistent, high-signal context files (architecture, conventions, current state) once per project, plus a routing convention (`AGENTS.md`) that tells agents which file to load for which kind of task. Instead of re-deriving project knowledge every time, an agent reads a compact, curated summary and gets to the actual work faster.

This is useful for teams and individuals running coding agents (Claude Code, or similar tools) against real codebases who want more consistent agent behavior and lower per-task token costs, without maintaining traditional, exhaustive documentation.

## Core files

This repository currently focuses on generating and maintaining:

* `.ai/architecture.md` — how the system works
* `.ai/conventions.md` — how code should be written in this repository
* `.ai/current-state.md` — what is happening in the project right now
* `AGENTS.md` and/or `CLAUDE.md` — tells coding agents when to load each context file

The `.ai/` files should contain compressed, high-signal project knowledge rather than exhaustive documentation.

## Recommended project structure

```text
AGENTS.md    # and/or CLAUDE.md

.ai/
  architecture.md
  conventions.md
  current-state.md
```

## Updating the router file

The routing convention can live in `AGENTS.md`, `CLAUDE.md`, or both, depending on which coding agent(s) you use:

* Use `AGENTS.md` for agent-agnostic tooling, or when multiple agents share the same routing rules.
* Use `CLAUDE.md` when the routing rules are specific to Claude Code (e.g. referencing Claude Code features like hooks or skills).
* If a project uses both files, keep the context-loading section in one and reference it from the other rather than duplicating it, so the routing rules don't drift out of sync.

Add a context-loading section to whichever file applies to your setup:

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

Instead, `AGENTS.md`/`CLAUDE.md` should act as a router that tells the agent which context is relevant to the task.

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

