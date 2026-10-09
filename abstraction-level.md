# Abstraction Level

## Project Abstraction Level

This project operates at the **microservices architecture** level with **gRPC** as the primary inter-service
communication protocol. The architecture is layered, with each layer having specific responsibilities and boundaries.

### Abstraction Layers (Top-Down)

#### 1. **Presentation Layer** (gateway)

- **Level**: Application boundary
- **Responsibility**: External client interaction
- **Details**:
    - HTTP entry point for all external clients
    - Request routing and proxying to internal services
    - Basic validation and transformation
    - Health check endpoints (readyz, livez, full-health)
    - Proxy orchestration calls (generic method routing)
    - REST-to-gRPC translation where needed
    - Aggregation of multiple service responses

#### 2. **Business Logic Layer** (services)

- **Level**: Service boundary
- **Responsibility**: Core domain logic
- **Sub-layers**:
    - **auth service**: Authentication, authorization, tokens, sessions
    - **profile service**: User profiles, ratings, levels, statistics
    - **matchmaking service**: Battle creation, matchmaking, join logic (write model)
    - **battle service**: Battle history/read model: Get, GetMany, TimeoutSweep (read model)
    - **engine service**: Domain-level battle logic, battle domain model
    - **streaming service**: Real-time WebSocket event broadcasting
    - **orchestration service**: Generic method routing proxy
    - **bot service**: AI opponent logic and move generation

#### 3. **Data Access Layer**

- **Level**: Infrastructure boundary
- **Responsibility**: Persistence and caching
- **Details**:
    - **PostgreSQL**: Primary data source via Prisma ORM (source of truth)
    - **Redis**: Cache for frequent reads, pub/sub for events
    - **pgbouncer**: Connection pooler between services and PostgreSQL
    - **Kafka**: Asynchronous event propagation
    - **Prisma ORM**: Type-safe database client

#### 4. **Infrastructure Layer**

- **Level**: Platform boundary
- **Responsibility**: Runtime support and operations
- **Details**:
    - **Docker**: Containerization for all services
    - **Kubernetes**: Deployment, scaling, self-healing
    - **Makefile targets**: Build, test, deployment helpers
    - **pnpm workspaces**: Monorepo package management
    - **TypeScript**: Type safety across all services

### Boundary Context

The project boundary encompasses:

**Included:**

- All microservices (gateway, auth, profile, orchestration, matchmaking, battle, engine, streaming, bot, pgbouncer,
  redis, kafka)
- Shared modules (logger, errors, kafka-manager, grpc-client-manager, pg-boss-manager)
- Proto definitions in `infra/proto/`
- Kubernetes manifests in `infra/k8s/`
- Docker configurations
- Git workflow and CI/CD processes
- Context repository as single source of truth

**Excluded:**

- External client systems (beyond gateway)
- Local developer machines (development environment only)
- Third-party services ( OAuth providers, etc. - only if integrated)
- Historical data outside current project scope

### Integration Points

#### gRPC Integration

- **Definition**: Protocol Buffers contracts in `infra/proto/`
- **Services**: auth, matchmaking, battle, profile, orchestration, streaming
- **Pattern**: All inter-service communication via gRPC
- **Code generation**: `ts-proto` generates TypeScript from .proto

#### Data Integration

- **PostgreSQL**: Source of truth (separate DB per service: matchmaking write, battle read)
- **Prisma ORM**: Type-safe queries and migrations (per-service schemas)
- **Redis**: Cache with TTL-based expiration (engine battle state)
- **Kafka**: Event topics for battle state changes (matchmaking.matched, battle.updated, battle.terminated)

#### External Integration

- **HTTP/gRPC gateway**: External client entry point
- **WebSocket streaming**: Real-time battle event subscription
- **Docker**: Service containerization
- **Kubernetes**: Production deployment and scaling

### Dependency Abstraction

```
High-level services (gateway, auth, profile, matchmaking, battle, streaming, bot)
       ↓ depend on
Lower-level infrastructure (db, cache, message broker, gRPC)
       ↓ depends on
Shared modules (logger, errors, kafka-manager, grpc-client-manager, pg-boss-manager)
       ↓
Proto definitions (contract layer)
```

**Rule**: No circular dependencies. Dependencies flow downward (from more specific to more generic). Shared modules must
remain framework-agnostic.

### Boundary Violations (Prohibited)

- Direct service-to-service imports outside gRPC
- Hardcoded service addresses or ports
- Bypassing the gateway for external access
- Storing context in multiple locations (context/ is the single source of truth)
- Skipping authentication/authorization checks
- Direct database queries outside Prisma ORM
- Bypassing Kafka for event propagation
- Skipping pgbouncer for PostgreSQL connections
