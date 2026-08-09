You are analyzing the current working state of an existing software repository to generate `.ai/current-state.md` for use by coding LLMs.

This file is **short-lived project memory**. Its purpose is to prevent another LLM from spending tokens rediscovering what is currently being worked on, what recently changed, and what temporary constraints or known issues matter right now.

Do NOT describe the overall architecture or coding conventions. Those belong in `.ai/architecture.md` and `.ai/conventions.md`.

## Investigate

Use the strongest available evidence, including:

* current branch and branch name
* working tree / `git status`
* staged and unstaged changes
* `git diff`
* recent commits, roughly the last 10–20 when useful
* files changed recently
* TODO/FIXME comments relevant to active work
* failing tests, builds, lint, or type checks if available
* issue/ticket/task context if available
* existing `.ai/current-state.md`, if present

Prioritize **recent and active information** over historical information.

## Rules

1. Derive everything from evidence. Do not guess.
2. Be conservative about intent. A changed file does not necessarily prove a larger product decision.
3. Never invent "current decisions" from implementation details alone.
4. Include decisions only when supported by explicit documentation, task instructions, commit messages, tickets, comments, or similarly strong evidence.
5. Distinguish clearly between:

   * active work
   * completed recent work
   * known issues
   * unresolved questions
6. Do not turn this into a changelog.
7. Do not summarize every recent commit.
8. Do not duplicate stable architectural information from `architecture.md`.
9. Do not duplicate stable coding rules from `conventions.md`.
10. Remove stale information from the previous `current-state.md` when evidence shows it is no longer relevant.
11. Prefer exact file paths when they help another LLM continue the work.
12. Optimize aggressively for information density.
13. Target approximately 300–800 words. Use less whenever possible.
14. Every entry should help an LLM make a better decision about work happening **now**.

## Output format

Write only the contents of `.ai/current-state.md`.

Use this structure, omitting empty sections:

# Current State

## Active Work

Describe work that appears to be currently underway.

For each significant item, keep it compact:

### [Short description]

* Goal:
* Current status:
* Relevant files:
* Next likely step:

Only include "Next likely step" when it is directly supported by available evidence. Do not invent a roadmap.

## Working Tree

Summarize meaningful uncommitted changes.

Do not list every modified file unless each one matters.

Highlight:

* partially implemented features
* unfinished refactors
* migrations in progress
* temporary code
* tests added but not passing
* files whose changes are tightly coupled

Omit this section when the working tree is clean.

## Recent Changes

Summarize only recent completed changes that are likely to affect upcoming work.

Focus on changes another LLM might otherwise fail to know about.

Example:

* Inbox refresh moved from polling toward SSE.
* Integration client creation was centralized in `lib/integrations/...`.
* Database model X was replaced by Y.

Do not reproduce commit history.

## Known Issues

List current known bugs, regressions, failing tests, incomplete behavior, or technical problems supported by evidence.

Use:

* **Issue:** concise description

  * Evidence:
  * Relevant area:

Keep evidence extremely brief.

## Current Decisions and Constraints

Include only explicit decisions or constraints that materially affect implementation.

Examples:

* Near-real-time synchronization is currently a hard requirement.
* New work should target provider X; provider Y remains only for backward compatibility.
* Migration Z must remain backward compatible until rollout completes.

Do not infer preferences merely because one implementation currently exists.

## Unresolved Questions

List important questions that appear genuinely unresolved and could affect implementation.

Only include questions supported by evidence.

Do not manufacture architectural discussion points.

## Temporary / Transitional State

Include this section only when the repository is between two implementations or architectures.

Examples:

* polling and SSE currently coexist during migration
* both old and new database schemas are active
* a legacy provider is being phased out
* feature exists behind a temporary flag

Explain which implementation should be treated as current when evidence makes that clear.

## Final quality check

Before producing the file:

* Delete anything that describes stable architecture rather than current state.
* Delete anything that belongs in coding conventions.
* Delete completed historical work that no longer affects current development.
* Delete speculative intentions.
* Verify referenced paths exist.
* Prefer five useful facts over twenty weak ones.
* Make sure stale entries from a previous `current-state.md` are removed.
* Make the file useful to an LLM starting a coding session today with no knowledge of recent work.
* Keep it substantially shorter than `architecture.md` and `conventions.md`.

