You are analyzing an existing software codebase to create a compact, high-signal `.ai/conventions.md` file for use by coding LLMs.

Your goal is NOT to document generic best practices. Your goal is to identify the conventions this repository actually follows so another LLM can modify the code without introducing inconsistent patterns.

Inspect the codebase, infer repeated implementation patterns, and write `.ai/conventions.md`.

## Objectives

The document should help an LLM answer:

* Where should new code go?
* How should new features be structured?
* How are modules named and organized?
* How is data fetched and mutated?
* How is database access performed?
* How are errors handled?
* How are APIs structured?
* How is validation performed?
* How is state managed?
* How are components structured?
* How are tests written?
* What patterns should be reused instead of reinvented?
* What patterns should explicitly be avoided?

## Core rule

Document **observed repository conventions**, not your preferred conventions.

If the codebase is inconsistent, say so rather than inventing a standard.

For example:

```md
Authentication checks are usually performed in service functions, but several
older API routes perform them directly. Prefer the service-layer pattern used
by newer modules.
```

Only make a recommendation like this when the repository itself provides strong evidence that one pattern is the intended/current approach.

## Rules

1. Derive conventions from the actual codebase.
2. Do not write generic software-engineering advice.
3. Prefer rules supported by multiple examples.
4. Include exact file paths as representative examples.
5. Do not enumerate every instance of a pattern.
6. Keep examples short.
7. Do not include large code snippets.
8. Distinguish clearly between:

   * established conventions
   * emerging/newer conventions
   * legacy patterns
   * genuine inconsistencies
9. Do not infer conventions solely from generated files, dependencies, or framework defaults.
10. Avoid documenting formatting rules already enforced automatically by Prettier, ESLint, Ruff, etc., unless they materially affect how code should be written.
11. Optimize aggressively for information density.
12. Target approximately 1,000–2,000 words. Use less if the repository has few meaningful conventions.
13. This file will be repeatedly read by coding LLMs, so every unnecessary token has an ongoing cost.

## Investigation strategy

Do not read every file sequentially.

Use repository structure, code search, representative modules, recent code, tests, and configuration to identify repeated patterns.

Inspect enough examples to understand conventions around:

* file and directory organization
* naming
* imports and module boundaries
* component structure
* server/client separation
* API handlers
* services/domain logic
* database access
* schemas and validation
* data fetching
* mutations
* state management
* async/background work
* error handling
* logging
* authentication and authorization
* third-party integrations
* configuration
* testing
* types
* styling, if relevant
* feature flags, caching, events, or other recurring infrastructure

Pay particular attention to recently modified code, since it may represent the current preferred pattern better than legacy modules.

## Output format

Write only the contents of `.ai/conventions.md`.

Use the following structure, omitting sections that do not meaningfully apply.

# Coding Conventions

## Guiding Principles

List only the most important repository-specific principles.

Keep this very short.

Example:

* Keep route handlers thin; business logic belongs in `lib/services/`.
* Database access goes through Drizzle queries rather than raw SQL.
* Prefer server components unless client-side interactivity is required.

Do not include generic principles like "write clean code."

## File and Module Organization

Document:

* where features live
* where shared code lives
* where domain logic belongs
* where infrastructure code belongs
* colocated vs centralized patterns
* how new modules are typically structured

Include representative paths.

## Naming

Document only meaningful naming conventions for:

* files
* components
* functions
* hooks
* services
* database entities
* API routes
* schemas/types
* constants

Do not document obvious language conventions unless the repository uses them consistently and they matter.

## Imports and Dependencies

Describe:

* path alias usage
* relative import conventions
* barrel exports, if any
* dependency direction between layers
* modules that should not import each other
* preferred wrappers around third-party libraries

## TypeScript / Language Conventions

Document repository-specific practices such as:

* `type` vs `interface`
* inferred vs explicit return types
* schema-derived types
* nullability handling
* enums vs unions
* async patterns
* important compiler constraints

Only include patterns clearly demonstrated by the codebase.

## Components and UI

If applicable, document:

* component organization
* server vs client components
* composition patterns
* prop conventions
* UI primitives
* styling approach
* loading/error/empty states
* form patterns

Focus on conventions another LLM could easily violate.

## Data Fetching and Mutations

Describe the preferred paths for reading and changing data.

For example:

```text
UI
  → server action
    → service
      → database
```

or whatever the repository actually uses.

Mention representative files.

## Database Access

Document:

* where queries belong
* ORM usage
* transaction patterns
* repository/service abstractions
* schema conventions
* migration expectations
* whether direct database access from routes/components is allowed

## APIs and Route Handlers

Describe:

* route structure
* validation
* authentication
* response format
* error handling
* where business logic belongs
* conventions for internal vs public endpoints

## Validation

Document:

* validation library
* where schemas live
* whether schemas are shared between client/server
* parsing conventions
* handling of invalid input

## Error Handling

Describe actual patterns for:

* domain errors
* API errors
* user-facing errors
* expected vs unexpected failures
* retries
* logging

Include representative paths where useful.

## Authentication and Authorization

Document conventions such as:

* where authentication is checked
* how current-user/session information is accessed
* where authorization belongs
* organization/tenant scoping
* helper functions that should be reused

Avoid duplicating the broader authentication architecture from `architecture.md`; focus on implementation conventions.

## Background Work and Events

If applicable, document conventions around:

* queues
* jobs
* webhooks
* events
* retries
* idempotency
* scheduled work
* real-time notifications

## External Integrations

Describe recurring patterns for integrating external services:

* wrappers/adapters
* client initialization
* configuration
* error normalization
* webhook handling
* mocks in tests

Do not list integrations unless they demonstrate a convention.

## Logging and Observability

Document how the repository performs:

* structured logging
* error reporting
* tracing
* metrics
* debug logging

Mention what should and should not be logged if this is evident from the code.

## Testing

Document:

* test locations
* naming
* test framework
* unit vs integration expectations
* mocking conventions
* fixtures/factories
* database setup
* what typically deserves a test

Focus on how existing tests are written rather than generic testing philosophy.

## Preferred Patterns

Create a compact table:

| When doing this...    | Prefer this pattern | Example |
| --------------------- | ------------------- | ------- |
| Adding business logic | Service module      | `...`   |
| Reading from the DB   | ...                 | `...`   |
| Validating API input  | ...                 | `...`   |

Include approximately 5–15 high-value entries.

## Avoid These Patterns

Only list patterns that the repository gives evidence should not be introduced.

Examples:

* Do not access the database directly from React client components.
* Do not instantiate third-party SDK clients inside individual handlers.
* Do not duplicate Zod schemas when an existing shared schema can be reused.
* Do not add new code following the legacy `pages/api` pattern.

Do not create prohibitions based purely on personal preference.

## Legacy / Transitional Patterns

If the repository contains multiple generations of architecture or conventions, explain them concisely.

Example:

```md
`lib/legacy-api/` uses direct database calls from handlers. Newer modules use
`services/` and should be treated as the preferred pattern for new work.
```

This section is especially important because an LLM may otherwise copy the wrong example.

## Known Inconsistencies

List only inconsistencies that could reasonably mislead another coding LLM.

Do not turn this into a code-quality audit.

## Final quality check

Before producing the file:

* Remove generic advice that could apply to any repository.
* Remove rules already obvious from formatter/linter configuration.
* Verify referenced paths exist.
* Prefer one representative example over several redundant examples.
* Make sure each rule helps an LLM make an implementation decision.
* Clearly distinguish current conventions from legacy patterns.
* Do not duplicate architectural information already better suited for `.ai/architecture.md`.
* Optimize for an LLM modifying this repository with minimal additional exploration.

