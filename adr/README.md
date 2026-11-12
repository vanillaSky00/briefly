# Architecture Decision Records

Each technical choice is documented in its own ADR with context, decision, and consequences.

## Architecture

```mermaid
graph TB
    subgraph "Driving Side (In)"
        LW[LINE Webhook<br/><i>@LineMessageHandler</i>]
        SCH[Scheduler<br/><i>Cron / Manual trigger</i>]
    end

    subgraph "Application Layer"
        AS[ArticleService]
        AFS[AggregateFeedService]
    end

    subgraph "Domain"
        FI((FeedItem))
        UFC["AggregateFeedUseCase<br/><i>port in</i>"]
        CSP["ContentSourcePort<br/><i>port out</i>"]
    end

    subgraph "Driven Side (Out)"
        HNA[HackerNewsAdapter]
        GA[GuardianAdapter]
        REPO[ArticleJpaRepository<br/><i>Spring Data JPA</i>]
    end

    subgraph "External Systems"
        HN_API[Hacker News API]
        GD_API[Guardian API]
        PG[(PostgreSQL)]
        LINE[LINE Platform]
    end

    LW --> AS
    SCH --> AFS
    AFS -.implements.-> UFC
    AFS --> CSP
    HNA -.implements.-> CSP
    GA -.implements.-> CSP
    HNA --> HN_API
    GA --> GD_API
    AS --> REPO
    REPO --> PG
    LW --> LINE
```

## Request Flow

When a user messages the LINE bot, here's what happens end to end:

```mermaid
sequenceDiagram
    participant U as User
    participant LINE as LINE Platform
    participant WH as LineWebhookHandler
    participant AS as ArticleService
    participant DB as PostgreSQL
    participant DR as DigestRenderer

    U->>LINE: Send message
    LINE->>WH: POST /callback (MessageEvent)
    WH->>AS: latestOfEach(["HN","GUARDIAN"], 5)
    AS->>DB: Native query with ROW_NUMBER()<br/>PARTITION BY source
    DB-->>AS: List<ArticleEntity>
    AS-->>WH: articles
    WH->>DR: toTextMessages(articles)
    DR-->>WH: List<Message> (chunked ≤ 4500 chars)
    WH->>LINE: replyMessage(token, messages)
    LINE-->>U: 📚 New Articles digest
```

## Decisions

- [ADR 0001: JPA / Hibernate](0001-jpa-hibernate.md)
- [ADR 0002: HikariCP](0002-hikaricp.md)
- [ADR 0003: Flyway](0003-flyway.md)
- [ADR 0004: OpenFeign](0004-openfeign.md)
- [ADR 0005: LINE Bot Webhook](0005-line-bot-webhook.md)
