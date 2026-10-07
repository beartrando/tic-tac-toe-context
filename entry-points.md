# Entry Points

## API Entry Points (Expanded)

### HTTP/gRPC Gateway Endpoints

| Category | Endpoint | Method | Path | Description | Auth Required |
|----------|----------|--------|------|-------------|---------------|
| **Authentication** | Anonymous sign-in | POST | `/auth/anonymous-sign-in` | Sign in with device ID | No |
| | Refresh tokens | POST | `/auth/refresh-tokens` | Refresh access token | No (with refresh token) |
| | Logout | POST | `/auth/logout` | Revoke tokens | Yes (access token) |
| | Get user | GET | `/auth/user/:id` | Retrieve user info | Yes |
| **Battles** | Create battle | POST | `/battle/init` | Initialize new battle | Yes |
| | Make move | POST | `/battle/move` | Submit move in battle | Yes |
| | Leave battle | POST | `/battle/leave` | Exit battle | Yes |
| | Get battle | GET | `/battle/:id` | Retrieve battle state | Yes |
| | Get many battles | GET | `/battle` | List battles | Yes |
| | Battle timeout | POST | `/battle/timeout-sweep` | Check for timed-out battles | Admin |
| **Profiles** | Get profile | GET | `/profile/:id` | Retrieve profile | Yes |
| | Get by user | GET | `/profile/user/:userId` | Profile by user ID | Yes |
| | Get or create | POST | `/profile/or-create` | Create if not exists | Yes |
| **Health** | Ready check | GET | `/health/readyz` | Readiness probe | No |
| | Live check | GET | `/health/livez` | Liveness probe | No |
| | Full health | GET | `/health/full-health` | Detailed health status | No |
| **Proxy/Orchestration** | Generic method | POST | `/orchestration/proxy` | Route arbitrary method | Yes |

### gRPC Service Endpoints

| Service | Method | Request Type | Response Type | Description |
|---------|--------|-------------|---------------|-------------|
| **auth.Auth** | Logout | Empty | SuccessResponse | Revoke tokens |
| | RefreshTokens | RefreshTokensRequest | SessionResponse | Refresh access token |
| | AnonymousSignIn | AnonymousSignInRequest | SessionResponse | Sign in with device ID |
| | Get | UserIdRequest | UserResponse | Retrieve user info |
| **battle.Battle** | Get | BattleIdRequest | BattleObject | Get battle state |
| | GetMany | BattleIdsRequest | BattlesResponse | List battles |
| | Init | BattleInitRequest | BattleResponse | Initialize battle |
| | Leave | BattleLeaveRequest | BattleResponse | Leave battle |
| **profile.Profile** | GetByUser | UserIdRequest | ProfileResponse | Get profile by user |
| | Get | ProfileIdRequest | ProfileResponse | Get profile by ID |
| | GetOrCreate | Empty | ProfileResponse | Create if not exists |
| **orchestration.Orchestration** | Proxy | ProxyRequest | ProxyResponse | Generic method routing |

### WebSocket Events (Streaming Service)

| Event | Direction | Payload | Description |
|-------|-----------|---------|-------------|
| `BattleInit` | Init → Client | `{ battleId, playerIds, initialState }` | New battle created |
| `BattleStart` | Init → Client | `{ battleId, startData }` | Battle begins |
| `BattleMove` | Move → Client | `{ battleId, cellIdx, playerId, board }` | Move made by player |
| `BattleLeave` | Leave → Client | `{ battleId, leftBy }` | Player left battle |
| `BattleJoinBot` | Bot → Client | `{ battleId, botId, symbol }` | Bot joined battle |
| `Ping` | Either → Client | `{ type: 'ping' }` | Keep-alive ping |

### gRPC Streaming Messages

| Message | Type | Field | Description |
|---------|------|-------|-------------|
| `BattleRequest` | input oneof | `init: BattleInit` | Initialize battle via WS |
| | | `start: BattleStart` | Start battle via WS |
| | | `move: BattleMove` | Make move via WS |
| | | `ping: common.ping.Ping` | Keep-alive |
| | | `leave: BattleLeave` | Leave battle via WS |
| | | `connect_bot: BattleJoinBot` | Bot connection via WS |
| `BattleResponse` | output oneof | `battle: battle.BattleObject` | Battle state |
| | | `ping: common.empty.Empty` | Keep-alive |
| | | `error: common.error.ErrorObject` | Error response |

### Configuration Entry Points

| File | Purpose | Key Contents |
|------|---------|--------------|
| `infra/services/*/infrastructure/grpc/config/config.ts` | gRPC service configuration | Port, host, credentials, timeout settings |
| `infra/services/*/infrastructure/http/configs/http.server.config.ts` | HTTP server config | Port, CORS, body limits, middleware |
| `infra/services/*/infrastructure/db/config.ts` | Database configuration | Prisma connection, pool settings |
| `infra/services/*/infrastructure/cache/config/redis.config.ts` | Redis configuration | Host, port, TTL settings, pub/sub channels |
| `infra/services/*/infrastructure/job/config/*.ts` | pg-boss job configuration | Queue names, retry policies, interval settings |
| `infra/services/*/infrastructure/message/configs/*.ts` | Kafka message configuration | Topic names, producer/consumer settings |
| `infra/services/*/infrastructure/auth/config.ts` | Auth service configuration | Token expiry, JWT secrets, device ID settings |
| `infra/services/*/tsconfig.json` | TypeScript configuration | Path mappings, compiler options, included files |
| `infra/pnpm-workspace.yaml` | Monorepo workspace | Package overlays, workspace dependencies, overrides |
| `infra/package.json` | Root package config | root dependencies, scripts, workspace config |
| `infra/tsconfig.base.json` | Base TS config | Extends config, shared path mappings, common options |
| `infra/context/` | Project context | Single source of truth documentation |

### Application Entry Points (per service)

| Service | Entry File | Description |
|---------|------------|-------------|
| **gateway** | `src/app.ts` | Main Express/Koa/hono server, registers all routes |
| **auth** | `src/app.ts` | Express server, gRPC service registration, health endpoints |
| **battle** | `src/app.ts` | gRPC server, battle handlers, WebSocket integration |
| **engine** | `src/app.ts` | gRPC engine handler, battle domain operations |
| **profile** | `src/app.ts` | gRPC profile service, user validation |
| **orchestration** | `src/app.ts` | gRPC proxy, method routing, proxy-controller |
| **streaming** | `src/app.ts` | WebSocket server, battle event broadcasting |
| **bot** | `src/app.ts` | gRPC bot service, AI logic, streaming client |
| **pgbouncer** | N/A | Connection pooler, no app entry point |
| **redis** | N/A | Cache server, no app entry point |
| **kafka** | N/A | Message broker, no app entry point |

### Proto-Derived Entry Points

| Proto File | Generated Types | Usage |
|------------|----------------|-------|
| `infra/proto/auth.proto` | `AuthClient`, `SessionResponse`, `UserResponse` | Auth service gRPC calls |
| `infra/proto/battle.proto` | `BattleClient`, `BattleObject`, `BattlesResponse` | Battle service gRPC calls |
| `infra/proto/profile.proto` | `ProfileClient`, `ProfileObject`, `ProfileResponse` | Profile service gRPC calls |
| `infra/proto/orchestration.proto` | `OrchestrationClient`, `ProxyRequest`, `ProxyResponse` | Orchestration/gateway proxy |
| `infra/proto/streaming.proto` | `StreamingServiceClient`, `BattleRequest`, `BattleResponse` | Streaming/WebSocket calls |

### Key Entry Point Relationships

```
External Client
    ↓ (HTTP)
gateway service (src/app.ts)
    ↓ (routes)
├── auth service (gRPC) - auth operations
├── profile service (gRPC) - profile operations
├── battle service (gRPC) - battle operations
├── orchestration (gRPC Proxy) - generic method routing
└── streaming service (WebSocket) - real-time events

Each service:
    ↓ (gRPC)
shared modules (@shared/*, infra/proto/)

↓ (infrastructure)
PostgreSQL (via Prisma)
Redis (cache)
Kafka (events)
pgbouncer (connection pool)
```

### Development Entry Points

- `pnpm build` - Compile all TypeScript, generate proto clients
- `pnpm dev` - Start development with hot-reload
- Per-service: `pnpm build` in each service directory
- `make git-pull-all` - Pull all repositories from dev
- `make git-commit-and-push-all` - Commit and push all changes