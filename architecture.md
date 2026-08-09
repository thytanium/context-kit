You are analyzing an existing software codebase to create a compact, high-signal `architecture.md` file for use by coding LLMs.

Your goal is NOT to document everything. Your goal is to reduce how much of the repository another LLM needs to explore before making changes.

Inspect the repository thoroughly enough to understand its architecture, then create `.ai/architecture.md`.

## Objectives

The document should let an LLM quickly answer:

* What does this application do?
* What are the major architectural components?
* Where does application execution start?
* How does data flow through the system?
* Where does important business logic live?
* How are authentication and authorization handled?
* How is persistence handled?
* What external services does the application depend on?
* What asynchronous/background processing exists?
* What real-time/event-driven mechanisms exist?
* Where should I look when modifying a particular subsystem?

## Rules

1. Derive everything from the actual codebase. Do not guess.
2. Prefer architecture over implementation details.
3. Include exact file paths when they help another LLM navigate the repository.
4. Mention important functions, classes, schemas, routes, or modules only when they are architectural landmarks.
5. Do not enumerate every file.
6. Do not explain obvious framework conventions unless this repository uses them in a meaningful way.
7. Do not include large code snippets.
8. Do not include package descriptions that are irrelevant to the architecture.
9. Explicitly call out uncertainty when architecture cannot be confidently inferred.
10. Optimize aggressively for information density.
11. Target approximately 1,500–3,000 words. Use fewer if the architecture is simple.
12. This document will be injected into LLM coding sessions repeatedly, so every unnecessary token has an ongoing cost.

## Investigate

At minimum, inspect:

* repository structure
* package/dependency manifests
* framework configuration
* application entry points
* routing
* database schema and data-access layer
* authentication/authorization
* API/server boundaries
* major domain modules
* background jobs, queues, cron jobs, or workers
* event handling, webhooks, SSE, WebSockets, or pub/sub
* third-party integrations
* environment-variable usage
* tests where they reveal intended architectural boundaries
* deployment/infrastructure configuration if present

Use code search and dependency tracing rather than reading every file sequentially.

## Output format

Write only the contents of `architecture.md`.

Use this structure, omitting sections that genuinely do not apply:

# Architecture

## System Overview

A concise description of what the system does and its overall architectural style.

## Technology Stack

Only architecturally significant technologies.

## Repository Structure

A compact tree or list of the important directories and what responsibility each owns.

Example:

```text
app/              Next.js routes and server entry points
components/       UI components
lib/              Core application services and shared infrastructure
db/               Schema, migrations, and persistence
workers/          Background processing
```

Do not list directories that don't help understand the architecture.

## Runtime Architecture

Explain the major runtime components and how they interact.

For example:

```text
Browser
  → Next.js application
    → API/server actions
      → domain services
        → PostgreSQL

External service
  → webhook endpoint
    → persistence
    → event notification
    → connected clients
```

Prefer a compact text diagram when useful.

## Request and Data Flow

Describe the most important execution paths through the application.

Focus on flows an engineer is likely to modify.

## Domain Architecture

Identify the major business/domain concepts and where their logic lives.

For each major domain, use:

### [Domain]

* Responsibility:
* Key modules:
* Important relationships:
* Entry points:

Keep each concise.

## Persistence

Describe:

* database technology
* schema/model location
* ORM/query layer
* important entities and relationships
* transaction patterns if architecturally important
* any separate caches, stores, or warehouses

Do not reproduce the database schema.

## Authentication and Authorization

Describe the authentication flow, authorization model, and relevant architectural boundaries.

Include key files.

## APIs and Server Boundaries

Describe the API architecture and the distinction between client-side and server-side responsibilities.

Mention important route groups or server entry points.

## Background and Asynchronous Processing

Describe queues, scheduled tasks, workers, asynchronous jobs, retries, and related infrastructure.

If none exist, omit this section.

## Events and Real-Time Behavior

Describe webhooks, internal events, SSE, WebSockets, pub/sub, polling, or other real-time mechanisms.

Include both producers and consumers where identifiable.

## External Integrations

Use a compact table:

| Integration | Purpose | Main integration point |
| ----------- | ------- | ---------------------- |

Include only services that materially affect the application architecture.

## Configuration and Environment

Describe architecturally important configuration and environment variables by purpose.

Do NOT include secrets or secret values.

Group variables instead of listing every variable when possible.

## Architectural Conventions

Document repository-specific patterns an LLM should preserve when adding code.

Examples:

* where business logic belongs
* how database access is performed
* preferred server/client boundaries
* error handling patterns
* event publishing patterns
* how integrations are wrapped
* dependency direction between layers

Only include conventions demonstrated by the codebase.

## Navigation Guide

Create a high-value lookup table for future LLMs:

| If you need to change... | Start here |
| ------------------------ | ---------- |
| Authentication           | `...`      |
| Database schema          | `...`      |
| [major feature]          | `...`      |

Include approximately 5–15 entries depending on repository complexity.

## Architectural Risks / Important Constraints

Include only constraints that could cause an engineer or coding LLM to make an incorrect architectural change.

Examples:

* processes are serverless and cannot rely on durable in-memory state
* a particular API is eventually consistent
* multiple providers implement the same interface
* legacy and new implementations coexist
* a worker is the sole writer for a particular entity

Do not turn this into a general code-quality review.

## Unknowns

List only meaningful architectural questions you could not resolve from the repository.

If there are no meaningful unknowns, omit this section.

## Final quality check

Before producing the document:

* Remove details that can easily be discovered from a single file.
* Remove repeated information.
* Replace prose with compact tables or diagrams where clearer.
* Verify every referenced path exists.
* Make sure the document describes the current code, not what the architecture "should" be.
* Optimize for an LLM returning to this repository with zero prior context.
* Favor information that prevents future repository exploration.

