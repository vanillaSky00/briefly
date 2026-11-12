# ADR 0003: Use Flyway for schema migrations

Status: Accepted

## Context

The database schema must be reproducible across environments and evolve through changes tracked in version control.

## Decision

Manage the schema with Flyway migration scripts under `src/main/resources/db/migration/V*.sql`. Run migrations automatically on application startup before JPA initialization. Keep Hibernate schema generation disabled with `ddl-auto: none`.

## Consequences

- Schema changes are explicit SQL files that can be reviewed with the application code.
- New databases can be initialized by applying the migration history.
- Entity changes that affect the schema require a corresponding migration; Hibernate will not update the schema automatically.
