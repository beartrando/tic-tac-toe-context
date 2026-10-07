# Important Files

## Core Configuration Files

### Root Configuration
- `infra/package.json` - Root package configuration, pnpm workspace settings
- `infra/pnpm-workspace.yaml` - Monorepo workspace definitions and overrides
- `infra/tsconfig.base.json` - Base TypeScript configuration
- `infra/tsconfig.json` - Root TS config (extends base)

### Per-Service Configuration
Each service has its own configuration:
- `infra/services/*/package.json` - Service dependencies and scripts
- `infra/services/*/tsconfig.json` - Service TypeScript config
- `infra/services/*/tsconfig.test.json` - Test TS config
- `infra/services/*/nodemon.json` - Development watch config
- `infra/services/*/jest.config.js` - Jest test configuration
- `infra/services/*/docker-compose.yml` - Service-specific Docker compose (if applicable)
- `infra/services/*/Dockerfile` - Container definition
- `infra/services/*/jest.setup.ts` - Jest setup configuration

### Infrastructure Configuration
- `infra/k8s/base/` - Kubernetes base manifests (namespaces, CRDs)
- `infra/k8s/overlays/` - Environment-specific overlays (local, production)
- `infra/docker-compose.yml.dist` - Distributed development setup
- `infra/docker-compose.yml.deploy.dist` - Deployment Docker compose

### Context Repository
- `infra/context/` - Single source of truth for project context
- `infra/context/*.md` - Context documentation files
- `infra/wiki/` - Architectural and operational wiki documentation

### Key Source Files (per service)
- `infra/services/*/src/application/application.ts` - Main application composition root
- `infra/services/*/src/app.ts` - Entry point for service
- `infra/services/*/src/infrastructure/` - Infrastructure implementations
- `infra/services/*/src/domain/` - Domain entities and value objects
- `infra/services/*/src/application/use-cases/` - Application use cases
- `infra/services/*/src/domain/common/value-objects/` - Shared value objects

### Proto Definitions
- `infra/proto/common/` - Common messages (empty, error, response, key_value, ping)
- `infra/proto/auth.proto` - Auth service contracts
- `infra/proto/battle.proto` - Battle service contracts
- `infra/proto/profile.proto` - Profile service contracts
- `infra/proto/orchestration.proto` - Orchestration proxy contracts
- `infra/proto/streaming.proto` - Streaming/WebSocket contracts