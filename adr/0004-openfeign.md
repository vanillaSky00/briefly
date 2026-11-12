# ADR 0004: Use OpenFeign for external API clients

Status: Accepted

## Context

Hacker News and The Guardian require HTTP clients that deserialize JSON responses. The Guardian also requires an API key that must stay scoped to its client.

## Decision

Declare `HackerNewsClient` and `GuardianClient` as Java interfaces annotated with `@FeignClient`, using Spring Cloud OpenFeign for HTTP calls and JSON deserialization. Keep Guardian authentication and client settings in the per-client `GuardianFeignConfig`. Intentionally omit `@Configuration` so component scanning does not register its API-key `RequestInterceptor` globally.

## Consequences

- API endpoints are declared in client interfaces rather than handwritten HTTP request code.
- Guardian's API key is added only to Guardian requests.
- Client-specific configuration must remain scoped to its client to avoid affecting other integrations.
