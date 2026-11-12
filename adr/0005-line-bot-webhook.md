# ADR 0005: Deliver digests through a LINE Bot webhook

Status: Accepted

## Context

Users request news digests by messaging a LINE bot. The reply must format stored articles within the messaging limits handled by the integration.

## Decision

Use the LINE SDK's Spring Boot integration with `@LineMessageHandler` and `@EventMapping` in `LineWebhookHandler`. For each message event, query the latest five articles from each of Hacker News and The Guardian through `ArticleService`, render them with `DigestRenderer`, and reply using the event's reply token.

The renderer splits text into chunks of up to 4500 UTF-16 code units, leaving headroom below the 5000-character limit, and returns at most five messages per reply.

## Consequences

- Users receive digests directly in LINE from articles already stored in PostgreSQL.
- Deployment requires LINE channel credentials and a publicly reachable `/callback` webhook URL.
- The renderer returns only the first five chunks, so content beyond that is omitted.
