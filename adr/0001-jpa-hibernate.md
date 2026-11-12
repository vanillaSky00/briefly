# ADR 0001: Use JPA / Hibernate for article persistence

Status: Accepted

## Context

Articles need persistent storage, lookups by source and external ID or URL hash, and a query for the latest N articles from each source.

## Decision

Use Spring Data JPA with Hibernate to map `ArticleEntity` to the PostgreSQL `articles` table. Use JPQL for simple lookups and a native query with `ROW_NUMBER() OVER (PARTITION BY source)` to retrieve the latest N articles per source in a single round trip.

## Consequences

- Standard persistence operations use Spring Data repositories.
- The digest query avoids separate queries for each source and application-side grouping.
- The native query couples this repository operation to database SQL support; changing databases requires reviewing it.
