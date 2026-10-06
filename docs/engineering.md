# Engineering decisions

MicroGreenPilot is as much an engineering project as a product project. This page summarizes several principles that shape the implementation.

## 1. The backend is authoritative

The browser is useful for fast feedback, but it is not a trusted boundary.

Permissions, ownership, business validation, plan/feature entitlement, and durable state changes belong on the server.

That means a disabled button or hidden route is treated as UX, not authorization.

## 2. Durable state belongs in PostgreSQL

Important product state is persisted server-side instead of being modeled as browser storage.

PostgreSQL is used for relational integrity and transactional behavior, while Drizzle ORM provides typed application access.

Browser storage is reserved for narrowly scoped device-level preferences or transient UX state where appropriate.

## 3. Schema changes are explicit

The application uses explicit Drizzle migrations.

Ordinary application startup should validate and use the expected schema, not silently create or alter production tables.

That makes schema evolution reviewable and reduces differences between environments.

## 4. User preferences and operational settings are different concepts

A user's personal timezone is not automatically the timezone used to run a growing operation.

The same distinction applies to other account-level display preferences versus grow-space configuration.

Separating these concepts early avoids subtle scheduling bugs later.

## 5. Historical production behavior should remain reproducible

As the production planner develops, configuration that affects a production run is intended to be versioned where necessary.

For example, changing a crop schedule in the future should not silently rewrite the meaning of an already-created production run.

This is particularly important for backwards planning, harvest windows, and future analytics.

## 6. AI should assist deterministic product logic, not replace it

MicroGreenPilot includes planned AI-assisted crop analysis, but AI is not intended to own core production truth.

A useful distinction is:

```text
Deterministic system
--------------------
ownership
permissions
crop schedules
dates
capacity
tasks
production state
orders
harvest records

AI-assisted layer
-----------------
image observations
crop-health hints
readiness suggestions
anomaly detection
natural-language assistance
```

This keeps the product explainable and testable while still taking advantage of AI where probabilistic analysis is valuable.

## 7. Security is treated as product behavior

Authentication is not just a login screen.

Account recovery, email changes, provider linking, sessions, 2FA, administrative actions, and auditability are workflows with their own failure modes.

The project therefore tests both expected behavior and negative/security boundary cases.

## 8. Failure states are designed deliberately

A failed API request should not look identical to an empty successful result.

The product distinguishes states such as:

- loading
- empty
- validation warning
- blocking validation failure
- permission denied
- network failure
- save failure
- stale/concurrent state
- entitlement restriction

That improves both usability and debugging.

## 9. Internationalization is part of the architecture

English and Swedish support is built into the product rather than added as a final translation pass.

The same applies to date/time formats and measurement preferences.

Persisted data stays canonical; formatting belongs at the presentation boundary.

## 10. Accessibility is a default requirement

New UI work is expected to consider:

- keyboard navigation
- visible focus
- semantic headings
- accessible forms
- screen-reader labels
- status that does not rely on color alone
- reduced-motion preferences

Where an interaction such as drag-and-drop exists, an accessible alternative should also exist.

## 11. CI should provide confidence without wasting resources

The project's CI work is moving toward tiered and parallel execution rather than running every expensive check indiscriminately.

The engineering goal is to balance:

- useful feedback time
- test isolation
- reproducibility
- coverage
- artifact safety
- runner cost

See [Testing & CI](testing-and-ci.md).

## 12. Public portfolio and private development are separate concerns

The production-development repository stays private while MicroGreenPilot is actively evolving.

This public repository is intentionally curated. That makes it possible to show architecture and engineering quality without exposing:

- unfinished internal planning
- private issue/PR discussions
- operational runbooks
- security-sensitive implementation detail
- CI logs and internal artifacts

The public material should remain technically meaningful, not merely promotional, while still respecting that boundary.
