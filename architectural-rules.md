# Architectural Rules

## General Rules (Expanded)

### 1. Single Source of Truth

- **Rule**: Project context is stored exclusively in the `context/` repository.
- **Evidence**: Commit 38b9ee6 "колокейтед-контекст упразднён, context/ — единственный источник истины"
- **Action**: CONTEXT/ directories within service projects have been removed
- **Make/context.mk**: No longer needed, context updates go directly to repositories
- **Discovery**: Any detected CONTEXT/ copies under deletion

### 2. gRPC Contract First

- **Rule**: All inter-service communication must use Protocol Buffers defined in `infra/proto/`.
- **Detail**: Services must not directly import each other's code
- **Implementation**:
    - `auth.proto`, `battle.proto`, `profile.proto`, `orchestration.proto`, `streaming.proto`
    - `ts-proto` generates TypeScript from .proto definitions
    - Services use generated client/stub classes
- **Prohibited**: Direct TypeScript imports between services

### 3. Monorepo Structure

- **Rule**: All services reside in the pnpm monorepo under `infra/services/`.
- **Shared modules**: Under `infra/shared/`
- **Per-service locations**:
    - `infra/services/*/src/` - Source code
    - `infra/services/*/Dockerfile` - Container definition
    - `infra/services/*/package.json` - Dependencies and scripts
    - `infra/services/*/tsconfig.json` - TypeScript config
- **Root configuration**:
    - `infra/package.json` - Root package config, turbo setup
    - `infra/pnpm-workspace.yaml` - Workspace definitions and overrides
    - `infra/tsconfig.base.json` - Base TypeScript configuration

### 4. Feature Branch Workflow

- **Rule**: All development must occur in feature branches named `feature-<hash>--<human_name>`, targeting `dev` via
  Pull Request.
- **Branch naming**: `feature-c1da48-end--battle-status-timeout`, `feature-fb21c5-end--context-rework`, etc.
- **Base branch**: Always `dev`
- **PR target**: `feature-<hash>--<human_name>` → `dev`
- **Prohibited**: Direct commits to `dev`, manual merges

### 5. Ticket Context

- **Rule**: Each task must have an associated ticket. Context updates must relate to the current task or be reflected in
  Wiki for long-term value.
- **Workflow**:
    - DevOrc picks tickets from `Todo` column
    - Context loaded at iteration start
    - New knowledge reflected in Context, Issue, or Wiki based on longevity
    - Issue kept updated with current work status
- **Reflection hierarchy**:
    - Context: Working context, relates to current task
    - Issue: Current task-specific context
    - Wiki: Long-term architectural significance

### 6. Service Responsibility Boundaries (Detailed)

- **auth**: Authentication, authorization, tokens, sessions, device management
- **profile**: User profiles, ratings, levels, statistics, nickname management
- **matchmaking**: Battle creation, matchmaking, join logic, publishes `matchmaking.matched` event (write model)
- **battle**: Battle history/read model: Get, GetMany, TimeoutSweep, consumes `battle.updated`/`battle.terminated` (read
  model)
- **streaming**: WebSocket events, battle real-time updates, bot connection management
- **orchestration**: Method routing, proxy functionality, proxy-controller for gateway
- **gateway**: External API entry point, request routing, health checks
- **bot**: AI opponent logic, move generation, battle simulation, timeout handling
- **shared/cross-cutting**:
    - **logger**: Log formatting, structured logging, no business logic
    - **errors**: Error types and formatting, domain rules only
    - **kafka-manager**: Message format and routing, no event content logic
    - **grpc-client-manager**: gRPC connection management, no business logic
    - **pg-boss-manager**: Connection pooling and task management, no query logic

### 7. Dependency Direction

- **Rule**: Dependencies should flow from more specific to more generic modules.
- **Detail**: Shared modules should not depend on service-specific code
- **Graph**:
  ```
  services/* → shared/*
  shared/logger → (none)
  shared/errors → (none)
  shared/kafka-manager → (none)
  shared/grpc-client-manager → (none)
  shared/pg-boss-manager → (none)
  ```
- **No circular dependencies** allowed between services
- **Proto definitions** are the only allowed inter-service contracts
- **No direct service imports** - all communication via gRPC

### 8. Testing

- **Rule**: Tests are colocated with source code (`.spec.ts` files alongside `.ts` implementation files).
- **Exception**: Battle/streaming tests are colocated in `src` directory
- **Test types**:
    - Unit tests: Individual use cases and handlers
    - Integration tests: Database operations via Prisma
    - e2e tests: Full battle flow end-to-end
    - Property tests: Battle domain invariants
- **Timeout tests**: `battle-timeout-sweep.test.ts` is exception to colocated rule

### 9. Containerization

- **Rule**: All services must be Dockerizable.
- **Evidence**: Each service has its own `Dockerfile`
- **Dockerfile structure**:
    - Multi-stage build if needed
    - `node:20` base image (based on package.json engines)
    - `pnpm install` followed by `pnpm build`
    - `node dist/server.js` as entry point
- **Docker Compose**: `infra/docker-compose.yml.dist` for local development

### 10. Kubernetes Deployment

- **Rule**: All services have Kubernetes manifests in `infra/k8s/base/` and overlays in `infra/k8s/overlays/`.
- **Base manifests** (`infra/k8s/base/`):
    - Namespace definitions
    - ConfigMaps and Secrets
    - Deployments, Services, Ingress
    - Statefullsets for PostgreSQL, Kafka
- **Overlays** (`infra/k8s/overlays/`):
    - `local/` - Local development configuration
    - `production/` - Production configuration
- **Kustomize**: Base + overlay composition

## Prohibited Patterns (Expanded)

### 1. Direct Service-to-Service Imports

- **Anti-pattern**: `import { AuthService } from '../auth/service'`
- **Fix**: Use gRPC calls through generated clients
- **Example**: Instead of importing auth service directly, call `AuthService` via gRPC

### 2. Hardcoded Service Addresses

- **Anti-pattern**: `grpc.loadPackageDefinition(...)` with hardcoded ports
- **Fix**: Use environment variables or service discovery
- **Configuration**: `infra/services/*/infrastructure/grpc/config/config.ts`

### 3. Bypassing the Gateway

- **Anti-pattern**: External clients calling services directly via gRPC
- **Fix**: All external requests go through `gateway` service
- **Rationale**: Single entry point, routing, aggregation, protocol translation

### 4. Storing Context in Multiple Locations

- **Anti-pattern**: CONTEXT/ directories in service projects
- **Fix**: All context in `infra/context/` only
- **Historical**: Commit 38b9ee6 removed these; any detection → deletion

### 5. Skipping Authentication

- **Anti-pattern**: Calls to battle/profile without valid token
- **Fix**: All gateway endpoints validate auth tokens first
- **Flow**: Gateway → auth validation → service call

### 6. Direct Database Queries

- **Anti-pattern**: Raw SQL or Prisma queries bypassing service layer
- **Fix**: All data access through service use cases
- **Abstraction**: Prisma client behind repository pattern in domain

### 7. Bypassing Kafka

- **Anti-pattern**: Direct event publishing without going through kafka-manager
- **Fix**: All async events go through `@shared/kafka-manager`
- **Pattern**: Producer → Kafka topic → Consumer pattern

### 8. Skipping pgbouncer

- **Anti-pattern**: Direct PostgreSQL connections without connection pooling
- **Fix**: All DB access through services using pgbouncer port
- **Port**: Configured in service infrastructure configs

### 9. Hardcoded Protobuf Values

- **Anti-pattern**: Magic numbers from proto definitions
- **Fix**: Use generated TypeScript types from `.proto` files
- **Example**: `battleCellValue.CELL_X` instead of `1`

### 9. Colocated Test Exceptions

- **Anti-pattern**: Moving tests out of `.spec.ts` location
- **Fix**: Keep `.spec.ts` alongside `.ts`, battle tests in `src` are exception
- **Rule update** (commit 963c3df): "update architectural rules: .test.ts -> .spec.ts and tsconfig.test.json ->
  tsconfig.spec.json"

### 10. Write/Read Model Separation (CQRS-like)

- **Pattern**: Split write-heavy and read-heavy operations into separate services
- **Example**: `matchmaking` (write model) handles battle creation/join, publishes events
- **Example**: `battle` (read model) handles Get/GetMany, consumes events for read replica
- **Benefits**: Independent scaling, separate databases, clear responsibility boundaries
- **Implementation**: Each service has own Prisma schema and PostgreSQL database
- **Communication**: Async via Kafka events (`matchmaking.matched` → `battle.updated`/`battle.terminated`)

## Enforcement Mechanisms

### CI/CD Checks

- `pnpm build` must succeed
- `pnpm test` must pass all tests
- Proto schema validation
- TypeScript type checking

### Code Review

- PR must reference related Issue
- Architectural rule violations blocked
- Context updates reflected appropriately

### Automated Detection

- grep for `import` statements between services
- Check for CONTEXT/ directories
- Validate proto definitions compile
- Check tsconfig consistency

### Documentation

- Architectural rules in `architectural-rules.md`
- Context in `context/` repository
- Wiki for long-term decisions
- Git workflow in `git-workflow.md`
