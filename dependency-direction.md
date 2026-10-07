# Dependency Direction

## Service Dependencies (Expanded)

### Internal Service Dependencies (Detailed)

```
gateway → [auth, profile, orchestration, battle, streaming, engine]
auth → (none - root service, foundational)
profile → [auth]  -- validates tokens, retrieves user info
orchestration → (none - proxy only, routes method calls)
battle → [auth, profile, engine]  -- auth for token validation, profile for rating, engine for domain logic
streaming → [battle, profile, auth]  -- battle state, profile data, auth for connection auth
engine → [battle, profile, auth]  -- battle domain, profile data, auth for token checks
bot → [battle, profile, auth]  -- AI needs battle state, profile data, auth for validated moves
```

### Shared Module Dependencies (Expanded)

```
Shared modules are the foundation, depended on by all services:

shared/logger
  → (no dependencies - foundation module)
  ← depended on by: all services (auth, battle, bot, gateway, etc.)

shared/errors
  → (no dependencies - foundation module)
  ← depended on by: all services

shared/kafka-manager
  → (no dependencies - foundation module)
  ← depended on by: services that produce/consume Kafka events
     (battle, orchestration, profile)

shared/grpc-client-manager
  → (no dependencies - foundation module)
  ← depended on by: services that make gRPC calls
     (gateway, orchestration, bot, engine)

shared/pg-boss-manager
  → (no dependencies - foundation module)
  ← depended on by: services using pg-boss for job queue
     (auth, orchestration, potentially battle for timeouts)

### Dependency Rules (Expanded)

1. **No circular dependencies** allowed between services
   - Detected via import graph analysis
   - Violations block PR merge
   - Example of violation: A → B → C → A

2. **Services depend on shared modules**, not vice versa
   - Shared modules must remain framework-agnostic
   - No shared module imports from `infra/services/*/src/`
   - Shared modules may import from `node_modules` only

3. **Shared modules must remain framework-agnostic**
   - No TypeScript-specific types from services
   - No InversifyJS decorators in shared
   - No Prisma types in shared
   - Pure utility functions and types

4. **Proto definitions are the only allowed inter-service contracts**
   - All service-to-service communication via gRPC
   - No direct TypeScript imports between services
   - Even "related" services (battle + engine) communicate via gRPC

5. **No direct service imports** - all communication via gRPC
   - Even internally within the monorepo
   - Use `@shared/grpc-client-manager` for client creation
   - Configuration in `infrastructure/grpc/config/`

### Data Flow (Expanded)

#### Request/Response Pattern
```
Client → gateway (HTTP) → orchestration (Proxy) → target service (gRPC) → response
```
- gateway handles HTTP → gRPC translation
- orchestration handles generic method routing
- Target services expose gRPC endpoints
- Responses flow back through the same path

#### Event-Driven Pattern (Kafka)
```
Service A (producer) → @shared/kafka-manager → Kafka topic → @shared/kafka-manager → Service B (consumer)
```
- Battle state changes → `battle.updated` topic
- User created events → `user.created` topic
- Battle terminated → `battle.terminated` topic
- Consumers: streaming, profile update, stats refresh

#### Cache Pattern (Redis)
```
Service → Redis → Cache → Service (fast read)
```
- Battle state caching for quick access
- Profile data caching (rating, level)
- Session token caching
- TTL-based expiration for freshness

#### Connection Pooling (pgbouncer)
```
Service → pgbouncer → PostgreSQL
```
- All service DB connections go through pgbouncer
- Reduces connection overhead
- Configurable pool sizes per service
- Failover and retry logic

### Cross-Service Dependency Graph

```
graph TD
    subgraph "Foundation"
        direction(LR)
        shared/logger[shared/logger]
        shared/errors[shared/errors]
        shared/kafka-manager[shared/kafka-manager]
        shared/grpc-client-manager[shared/grpc-client-manager]
        shared/pg-boss-manager[shared/pg-boss-manager]
    end

    subgraph "Services"
        direction(LR)
        gateway[gateway]
        auth[auth]
        profile[profile]
        orchestration[orchestration]
        battle[battle]
        engine[engine]
        streaming[streaming]
        bot[bot]
    end

    connections
        gateway --> shared/logger
        auth --> shared/logger
        battle --> shared/logger
        bot --> shared/logger
        profile --> shared/logger
        orchestration --> shared/logger
        streaming --> shared/logger

        gateway --> shared/errors
        auth --> shared/errors
        battle --> shared/errors
        bot --> shared/errors
        profile --> shared/errors
        orchestration --> shared/errors
        streaming --> shared/errors

        gateway --> shared/grpc-client-manager
        orchestration --> shared/grpc-client-manager
        bot --> shared/grpc-client-manager
        engine --> shared/grpc-client-manager

        battle --> shared/kafka-manager
        orchestration --> shared/kafka-manager

        auth --> shared/pg-boss-manager
        orchestration --> shared/pg-boss-manager
```

### Import Restrictions (Enforced)

**Allowed imports per service**:

- **gateway**: `@shared/logger`, `@shared/errors`, `@shared/grpc-client-manager`, `@shared/kafka-manager`, service proto imports, `@infra/orchestration`
- **auth**: `@shared/logger`, `@shared/errors`, no shared grpc-client-manager (foundational), prisma types
- **profile**: `@shared/logger`, `@shared/errors`, `@shared/grpc-client-manager`, proto imports from auth
- **orchestration**: `@shared/logger`, `@shared/errors`, proto imports, service repos (db, message, job)
- **battle**: `@shared/logger`, `@shared/errors`, `@shared/kafka-manager`, `@shared/pg-boss-manager`, proto imports, engine service
- **engine**: `@shared/logger`, `@shared/errors`, proto imports, battle service repo, cache service
- **streaming**: `@shared/logger`, `@shared/errors`, proto imports from battle/profile/auth
- **bot**: `@shared/logger`, `@shared/errors`, proto imports from battle, profile, auth

**Forbidden imports**:
- Service A → Service B (where B ≠ shared/*)
- shared/* → infra/services/*/src/
- Direct proto import without ts-proto generation
- Circular: A → B → A (detected and blocked)

### Configuration-Dependency Relationship

```
service/config/ → service/infrastructure/ → service/repos/ → service/domain/ → service/application/
                                     ↓
                               service/infrastructure/grpc/
```

- Config files are the entry point, downward dependency
- Each layer depends only on the layer below it
- No backward (upward) dependencies allowed
- Violations detected during code review and CI

### Exception Handling

**Battle/Streaming test exception** (commit 963c3df):
- Rule: "update architectural rules: .test.ts -> .spec.ts and tsconfig.test.json -> tsconfig.spec.json"
- Battle timeout tests are intentionally colocated in `src/`
- This is the only exception to the colocated test rule
- All other tests must follow `.spec.ts` convention
- tsconfig.test.json → tsconfig.spec.json migration in progress

### Dependency Validation

**Pre-commit checks**:
- `git diff --stat` to detect new imports
- `grep` for forbidden import patterns
- Import graph validation

**CI checks**:
- `pnpm build` must succeed (includes proto compilation)
- `pnpm test` must pass
- Import graph analysis report
- Architectural rules validation

**Manual review**:
- PR must include architectural review
- Reviewer checks dependency direction
- Context updates must follow rules