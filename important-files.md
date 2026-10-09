# Important Files

## Core Configuration Files (Expanded)

### Root Configuration

| File                        | Purpose                        | Key Contents                                                                                         |
|-----------------------------|--------------------------------|------------------------------------------------------------------------------------------------------|
| `infra/package.json`        | Root package configuration     | `name: "game"`, `version: "1.0.0"`, devDependencies (ts-proto, turbo), packageManager (pnpm@11.9.0)  |
| `infra/pnpm-workspace.yaml` | Monorepo workspace definitions | Packages: `services/*`, `shared/*`; Overrides for @shared/* modules; `injectWorkspacePackages: true` |
| `infra/tsconfig.base.json`  | Base TypeScript configuration  | Extends for all services, shared path mappings, common compiler options                              |
| `infra/tsconfig.json`       | Root TS config                 | Extends `tsconfig.base.json`, includes all service directories                                       |
| `infra/turbo.json`          | Turbo build pipeline           | Task pipeline, cache configuration, dependency graph                                                 |
| `infra/Makefile`            | Build/deployment helpers       | Targets: `git-pull-all`, `git-commit-and-push-all`, `bip`, service-specific make targets             |
| `infra/README.md`           | Project documentation          | Overview, wiki reference, context guidelines                                                         |

### Per-Service Configuration

Each service has its own configuration directory:

| File                                  | Service       | Purpose                                                           |
|---------------------------------------|---------------|-------------------------------------------------------------------|
| `infra/services/*/package.json`       | All services  | Service dependencies, scripts (build, dev, test), ts-proto config |
| `infra/services/*/tsconfig.json`      | All services  | Service-specific TypeScript config, path mappings                 |
| `infra/services/*/tsconfig.test.json` | All services  | Test TypeScript config (being migrated to .spec.ts)               |
| `infra/services/*/tsconfig.spec.json` | All services  | New test config format (replacing .test.json)                     |
| `infra/services/*/nodemon.json`       | All services  | Development watch config, file watching patterns                  |
| `infra/services/*/jest.config.js`     | All services  | Jest test configuration, custom transformers                      |
| `infra/services/*/jest.setup.ts`      | All services  | Jest setup file, global test configurations                       |
| `infra/services/*/docker-compose.yml` | Some services | Service-specific Docker compose (if needed)                       |
| `infra/services/*/Dockerfile`         | All services  | Container definition, multi-stage build if needed                 |
| `infra/services/*/ .env*`             | All services  | Environment variables, dist vars (.env.dist)                      |
| `infra/services/*/.env.dist`          | All services  | Distributed environment variables template                        |

### Infrastructure Configuration

| File                                   | Purpose                   | Services                                                                  |
|----------------------------------------|---------------------------|---------------------------------------------------------------------------|
| `infra/k8s/base/`                      | Kubernetes base manifests | All services (namespace, ConfigMap, Secret, Deployment, Service, Ingress) |
| `infra/k8s/overlays/local/`            | Local environment overlay | Local DevOps configuration                                                |
| `infra/k8s/overlays/production/`       | Production overlay        | Production DevOps configuration                                           |
| `infra/docker-compose.yml.dist`        | Distributed dev setup     | All services for local development                                        |
| `infra/docker-compose.yml.deploy.dist` | Deployment docker compose | Deployment-specific configuration                                         |
| `infra/k8s/base/namespace.yaml`        | Namespace definitions     | Isolated environments (dev, staging, prod)                                |
| `infra/k8s/base/configmap.yaml`        | ConfigMaps                | Configuration for all services                                            |
| `infra/k8s/base/secrets.yaml`          | Secrets                   | TLS certs, API keys, database passwords                                   |

### Context Repository

| File                                    | Purpose                                    |
|-----------------------------------------|--------------------------------------------|
| `infra/context/`                        | Single source of truth for project context |
| `infra/context/structure.md`            | Project structure and service overview     |
| `infra/context/responsibility.md`       | Service responsibilities                   |
| `infra/context/purpose.md`              | Project purpose and goals                  |
| `infra/context/abstraction-level.md`    | Abstraction level description              |
| `infra/context/architectural-rules.md`  | Architectural rules and guidelines         |
| `infra/context/dependency-direction.md` | Service dependency graph                   |
| `infra/context/entry-points.md`         | API endpoints and integration points       |
| `infra/context/important-files.md`      | This file - important configuration files  |
| `infra/context/non-responsibility.md`   | Service boundary definitions               |
| `infra/context/git-workflow.md`         | Git branching model and PR process         |
| `infra/context/navigation.md`           | Table of contents for context repo         |
| `infra/context/parent.md`               | Parent/overview document                   |

### Key Source Files (per service)

| Layer              | File                                        | Purpose                                                          |
|--------------------|---------------------------------------------|------------------------------------------------------------------|
| **Application**    | `src/application/application.ts`            | Main application composition root, DependencyInjection container |
| **Composition**    | `src/composition/build-application.ts`      | InversifyJS container setup, binds interfaces to implementations |
| **App Entry**      | `src/app.ts`                                | Service entry point, starts HTTP/gRPC server, registers handlers |
| **Domain**         | `src/domain/`                               | Domain entities, value objects, business rules                   |
| **Use Cases**      | `src/application/use-cases/`                | Application layer use cases, business logic orchestration        |
| **Infrastructure** | `src/infrastructure/`                       | External implementations (DB, gRPC, cache, message, job)         |
| **GRPC Handlers**  | `src/infrastructure/grpc/handlers/`         | gRPC service handlers, request/response transformation           |
| **GRPC Clients**   | `src/infrastructure/grpc/clients/`          | gRPC client stubs for inter-service calls                        |
| **Repositories**   | `src/infrastructure/db/repositories/`       | Prisma-based data repositories                                   |
| **DB Service**     | `src/infrastructure/db/service.ts`          | Database service wrapper, connection management                  |
| **Config**         | `src/infrastructure/*/config.ts`            | Infrastructure-specific configuration (DB, gRPC, auth, etc.)     |
| **Message**        | `src/infrastructure/message/service.ts`     | Kafka/producer-consumer wrapper                                  |
| **Job Service**    | `src/infrastructure/job/service.ts`         | pg-boss job queue manager                                        |
| **WebSocket**      | `src/infrastructure/websocket/`             | WebSocket server/client implementations                          |
| **Timer**          | `src/infrastructure/timer/service.ts`       | Timer service for battle timeouts                                |
| **Cache**          | `src/infrastructure/cache/service.ts`       | Redis cache wrapper                                              |
| **State Store**    | `src/infrastructure/state-store/service.ts` | Cached state store interface + implementations                   |

### Proto Definitions

| File                                 | Package            | Purpose                                                       |
|--------------------------------------|--------------------|---------------------------------------------------------------|
| `infra/proto/common/empty.proto`     | `common.empty`     | `message Empty {}` - base for optional fields                 |
| `infra/proto/common/response.proto`  | `common.response`  | `SuccessResponse`, `ErrorObject` message types                |
| `infra/proto/common/key_value.proto` | `common.key_value` | `KeyValue` message for headers/query_params                   |
| `infra/proto/common/ping.proto`      | `common.ping`      | `Ping` message for WebSocket keep-alive                       |
| `infra/proto/auth.proto`             | `auth`             | Auth service gRPC contracts (Logout, RefreshTokens, etc.)     |
| `infra/proto/matchmaking.proto`      | `matchmaking`      | Matchmaking service gRPC contracts (Init)                     |
| `infra/proto/battle.proto`           | `battle`           | Battle service gRPC contracts (Get, GetMany) + enums/messages |
| `infra/proto/profile.proto`          | `profile`          | Profile service gRPC contracts                                |
| `infra/proto/orchestration.proto`    | `orchestration`    | Generic method routing Proxy service                          |
| `infra/proto/streaming.proto`        | `streaming`        | WebSocket battle events and messages                          |

### Generated/Contracts (per service)

| Directory                       | Service                       | Purpose                                                   |
|---------------------------------|-------------------------------|-----------------------------------------------------------|
| `src/contracts/proto/`          | battle, engine, profile, etc. | Local copies of proto definitions for convenience         |
| `src/contracts/proto/common/`   | All services                  | Common messages (empty, error, key_value, ping, response) |
| `src/contracts/proto/engine.ts` | battle                        | Engine-specific proto types                               |
| `src/contracts/proto/battle.ts` | battle                        | Battle-specific proto types                               |
| `src/contracts/proto/auth.ts`   | battle                        | Auth types for battle service                             |

### Shared Modules (per @shared/*)

| Module                              | Key Files                        | Purpose                                       |
|-------------------------------------|----------------------------------|-----------------------------------------------|
| `@shared/logger`                    | `src/utils/sleep.ts`, `index.ts` | Logging utilities, winston/pino configuration |
| `@shared/errors`                    | Error value objects              | Custom error types, error formatting          |
| `@shared/kafka-manager`             | Configs, producers, consumers    | Kafka integration wrappers                    |
| `@shared/grpc-client-manager`       | Config, client creation          | gRPC client instantiation and management      |
| `@shared/pg-boss-manager`           | Config, service                  | pg-boss job queue management                  |
| `@shared/errors/important-files.md` | Error file structure             | Error-related important files                 |

### Per-Service Important Files Pattern

```
infra/services/{service}/
├── Dockerfile          # Container definition
├── tsconfig.json       # TypeScript config
├── tsconfig.test.json  # Test config (migrating to .spec.ts)
├── tsconfig.spec.json  # New test config format
├── nodemon.json        # Dev watch config
├── jest.config.js      # Jest configuration
├── jest.setup.ts       # Jest global setup
├── package.json        # Service dependencies & scripts
├── .env.dist           # Environment template
├── .env                # Local env overrides
├── src/
│   ├── application/    # App composition root
│   ├── composition/    # DI container setup
│   ├── app.ts          # Entry point
│   ├── domain/         # Domain entities/value objects
│   ├── infrastructure/ # External impls
│   │   ├── grpc/       # gRPC service/clients
│   │   ├── db/         # Prisma/repositories
│   │   ├── cache/      # Redis wrapper
│   │   ├── message/    # Kafka wrapper
│   │   ├── job/        # pg-boss job queue
│   │   ├── timer/      # Battle timeout service
│   │   └── websocket/  # WebSocket server/client
│   ├── application/use-cases/ # Business use cases
│   ├── contracts/      # Proto-generated types
│   └── utils/          # Utilities (sleep, etc.)
├── infrastructure/
│   ├── grpc/           # gRPC config, service, clients
│   ├── db/             # DB config, service, repos
│   ├── cache/          # Redis config
│   ├── message/        # Kafka config, consumers
│   ├── job/            # pg-boss config, service
│   ├── timer/          # Timeout config, callbacks
│   └── websocket/      # WS config, server
├── healthcheck.js      # Health check endpoint
├── nodemon.json        # Dev server config
└── Dockerfile          # Container definition
```

**matchmaking service** (write model):

- `src/domain/matchmaking/` — BattleEntity, VOs (BattleId, BattleStatus, Board, PlayerIds)
- `src/application/use-cases/matchmaking/init.ts` — Init use case (create/join)
- `src/infrastructure/db/repositories/prisma-battle.repository.ts` — Write repo
- `src/infrastructure/message/producers/` — Kafka producer for `matchmaking.matched`

**battle service** (read model):

- `src/application/use-cases/battle/` — Get, GetMany, TimeoutSweep, Terminate
- `src/infrastructure/message/consumers/` — Kafka consumers for `battle.updated`, `battle.terminated`
- `src/infrastructure/db/repositories/prisma-battle.repository.ts` — Read repo

### Build Artifacts

| Artifact                | Location                                | Description                        |
|-------------------------|-----------------------------------------|------------------------------------|
| `dist/`                 | `infra/services/*/dist/`                | Compiled TypeScript output         |
| `dist/server.js`        | Entry point for Node runtime            | `main` field in package.json       |
| `.js` source maps       | `dist/*.map`                            | Source maps for debugging          |
| `proto/` generated      | `infra/services/*/src/contracts/proto/` | TypeScript types from .proto files |
| `.spec.ts` / `.test.ts` | `src/**/*.spec.ts`                      | Test files (colocated convention)  |

### Service Entry Points (per service)

```
Entry: infra/services/{service}/src/app.ts
├── Imports Application class
├── Imports build-application.ts (DI container)
├── Creates Application instance
├── Calls application.start()
├── Handles process signals (SIGINT, SIGTERM)
└── Graceful shutdown via shutdown() function
```

## Critical Path Files

1. **`infra/context/structure.md`** - Project overview and service list
2. **`infra/context/architectural-rules.md`** - Key architectural constraints
3. **`infra/pnpm-workspace.yaml`** - Monorepo workspace configuration
4. **`infra/tsconfig.base.json`** - Base TypeScript configuration
5. **`infra/services/*/src/application/application.ts`** - App composition root
6. **`infra/services/*/src/composition/build-application.ts`** - DI container
7. **`infra/proto/*.proto`** - gRPC contract definitions
8. **`infra/services/*/Dockerfile`** - Container definitions
9. **`infra/k8s/base/*`** - Kubernetes manifests
10. **`infra/context/`** - Single source of truth documentation
