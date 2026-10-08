# Structure

## Project Overview

Distributed tic-tac-toe game with microservices architecture. The project uses a monorepo structure managed with pnpm and gRPC for inter-service communication. All services are typed with TypeScript and use InversifyJS for dependency injection.

### Core Services (10 total)

| Service | Purpose | Key Dependencies |
|---------|---------|------------------|
| **gateway** | HTTP entry point, request routing, proxy to internal services | `@shared/logger`, `@shared/errors`, grpc clients, orchestration client |
| **auth** | Authentication, authorization, token management, user sessions | Prisma, `@shared/logger`, `@shared/errors`, `@shared/kafka-manager`, `@shared/pg-boss-manager`, `@shared/grpc-client-manager` |
| **profile** | User profile management, ratings, levels, statistics | Prisma, auth service, `@shared/logger`, `@shared/kafka-manager` |
| **orchestration** | Generic method routing proxy, proxy-controller for gateway | `@shared/logger`, `@shared/errors`, db, message, job services |
| **matchmaking** | Battle creation, matchmaking, join logic, publishes matchmaking.matched event | Prisma, `@shared/logger`, `@shared/errors`, `@shared/kafka-manager`, `@shared/pg-boss-manager`, timer service |
| **battle** | Battle history/read model: Get, GetMany, TimeoutSweep, consumes battle.updated/terminated | Prisma, `@shared/logger`, `@shared/errors`, `@shared/kafka-manager`, `@shared/pg-boss-manager` |
| **streaming** | WebSocket real-time communication, battle event broadcasting | auth service, profile service, matchmaking service, battle service, timer, grpc-stream, websocket server |
| **engine** | Domain-level battle logic, domain events, battle domain model | battle service, cache, state-store, auth, profile, `@shared/logger` |
| **pgbouncer** | PostgreSQL connection pooling | - |
| **redis** | Cache, pub/sub for distributed events | - |
| **kafka** | Message broker for async events (battle state changes, user created) | - |
| **bot** | AI opponent logic, move generation, battle simulation | battle service, profile service, auth, `@shared/logger` |

### Shared Modules

| Module | Purpose |
|--------|---------|
| **@shared/logger** | Structured logging using winston/pino |
| **@shared/errors** | Custom error types and handling |
| **@shared/kafka-manager** | Kafka producer/consumer wrappers |
| **@shared/grpc-client-manager** | gRPC client creation and management |
| **@shared/pg-boss-manager** | Job queue management using pg-boss |
| **@shared/pg-boss-manager** | PostgreSQL-based job scheduling |

### API Contracts (gRPC)

Services communicate via Protocol Buffers defined in `infra/proto/`:

**auth.proto**:
- `Auth.service`: `Logout`, `RefreshTokens`, `AnonymousSignIn`, `Get`

**matchmaking.proto**:
- `Matchmaking.service`: `Init`
- Messages: `MatchmakingInitRequest`, `MatchmakingResponse`
- Reuses enums/messages from `battle.proto` (`BattleObject`, `BattleCellValue`, `BattleStatus`)

**battle.proto**:
- `Battle.service`: `Get`, `GetMany`
- Enums: `BattleCellValue` (CELL_EMPTY=0, CELL_X=1, CELL_O=2), `BattleStatus`
- Messages: `BattleObject`, `BattleIdsRequest`, `BattleIdRequest`

**profile.proto**:
- `Profile.service`: `GetByUser`, `Get`, `GetOrCreate`
- Messages: `ProfileObject` (id, user_id, nickname, level, rating, experience, created_at, updated_at)

**orchestration.proto**:
- `Orchestration.service`: `Proxy` (generic method routing via method name + path + headers + query + body)

**streaming.proto**:
- `Streaming.service`: WebSocket battle events (`BattleInit`, `BattleStart`, `BattleMove`, `BattleLeave`, `BattleJoinBot`)
- Messages: `BattleRequest` (oneof input: init, start, move, ping, leave, connect_bot), `BattleResponse` (oneof output: battle, ping, error)

### Infrastructure

- **PostgreSQL**: Primary data source via Prisma ORM
- **Redis**: Cache and pub/sub for real-time events
- **Kafka**: Asynchronous event propagation (battle updated, user created, battle terminated)
- **pgbouncer**: Connection pooler between services and PostgreSQL
- **Docker**: Containerization for all services
- **Kubernetes**: Deployment and orchestration

### Project Structure (Monorepo)

```
infra/
├─ proto/                    # gRPC proto definitions
├─ services/                 # Microservices (11 services + bot)
│  ├─ auth/                  # Auth service
│  ├─ matchmaking/           # Matchmaking service (battle creation/join)
│  ├─ battle/                # Battle history/read service
│  ├─ engine/                # Engine domain service
│  ├─ profile/               # Profile service
│  ├─ gateway/               # Gateway service
│  ├─ orchestration/         # Orchestration proxy
│  ├─ streaming/             # WebSocket streaming
│  ├─ bot/                   # Bot AI service
│  ├─ pgbouncer/             # Connection pooler
│  ├─ redis/                 # Cache service
│  ├─ kafka/                 # Message broker
│  └─ shared/                # Shared modules
│     ├─ logger/             # Logging
│     ├─ errors/             # Error types
│     ├─ kafka-manager/      # Kafka wrappers
│     ├─ grpc-client-manager/ # gRPC client manager
│     └─ pg-boss-manager/    # Job queue manager
├─ wiki/                     # Documentation wiki
├─ context/                  # Single source of truth for project context
├─ k8s/                      # Kubernetes manifests
├─ docker-compose*.yml       # Docker configurations
├─ package.json              # Root package config
└─ pnpm-workspace.yaml       # Workspace configuration
```

### Key Integration Points

1. **gateway ↔ services**: Via gRPC (internal) and HTTP (external)
2. **gateway ↔ orchestration**: Proxy routing for arbitrary method calls
3. **matchmaking ↔ engine**: matchmaking publishes `matchmaking.matched` → engine consumes via Kafka
4. **battle ↔ engine**: battle consumes `battle.updated`, `battle.terminated` from engine
5. **battle ↔ streaming**: Real-time WebSocket event broadcasting (read model)
6. **battle ↔ kafka**: Async events consumer (battle updated, terminated, timeout)
7. **matchmaking ↔ kafka**: Async events producer (matchmaking.matched)
8. **profile ↔ auth**: User authentication and profile association
9. **bot ↔ battle/profile**: AI opponent integration
10. **streaming ↔ matchmaking/battle**: Init/Join via matchmaking, Get/GetMany via battle
11. **All services ↔ shared/***: Common utilities (logger, errors, kafka-manager, grpc-client-manager, pg-boss-manager)
12. **Services ↔ PostgreSQL**: Via Prisma ORM through pgbouncer pool (each service has own DB)
13. **Services ↔ Redis**: For caching and pub/sub

### Development Commands

- `pnpm build` - Build all services
- `pnpm dev` - Start development mode with turbopack/watcher
- `make git-pull-all` - Pull all repositories from dev branch
- `make git-commit-and-push-all` - Commit and push all changed repos
- Per-service: `pnpm build` in each service directory