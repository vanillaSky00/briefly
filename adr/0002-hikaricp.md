# ADR 0002: Use HikariCP for connection pooling

Status: Accepted

## Context

The JPA layer needs reusable PostgreSQL connections with a bounded pool and a timeout when connections are unavailable.

## Decision

Use HikariCP, Spring Boot's default connection pool. The development and Compose profiles configure a maximum pool size of 10, a minimum idle count of 2, and a connection timeout of 20 seconds.

## Consequences

- Connections are reused between database operations.
- Pool capacity and connection wait time are bounded by the profile configuration.
- Higher concurrency or additional service instances require reviewing pool sizes against PostgreSQL's connection capacity.
