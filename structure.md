# Structure

## Project Overview

Distributed tic-tac-toe game with microservices architecture. The project uses a monorepo structure managed with pnpm and gRPC for inter-service communication.

### Services

| Service | Purpose |
|---------|---------|
| **gateway** | Entry point for external requests, handles routing and proxying to other services |
| **auth** | Authentication and authorization (anonymous sign-in, token refresh, user retrieval) |
| **profile** | User profile management (get, get-or-create, get by user) |
| **orchestration** | Proxy service for generic method routing |
| **battle** | Tic-tac-toe game logic (create battles, make moves, leave, get battle state) |
| **streaming** | WebSocket-based real-time communication for battle events |
| **pgbouncer** | Connection pooler for PostgreSQL |
| **redis** | Cache and pub/sub system |
| **kafka** | Message broker for asynchronous events |
| **bot** | Bot service for AI opponents |

### API Contracts (gRPC)

Services communicate via Protocol Buffers defined in `infra/proto/`:

- **auth.proto**: `Logout`, `RefreshTokens`, `AnonymousSignIn`, `Get`
- **battle.proto**: `Get`, `GetMany`, `Init`, `Leave`
- **profile.proto**: `GetByUser`, `Get`, `GetOrCreate`
- **orchestration.proto**: `Proxy` (generic method routing)
- **streaming.proto**: WebSocket battle events (`BattleInit`, `BattleStart`, `BattleMove`, `BattleLeave`, `BattleJoinBot`)

### Technology Stack

- **Language**: TypeScript
- **Runtime**: Node.js
- **Dependency Injection**: InversifyJS (implied by composition roots)
- **Protobuf**: gRPC communication
- **Monorepo**: pnpm workspaces
- **Containerization**: Docker + Docker Compose
- **Kubernetes**: Deployment manifests in `infra/k8s/`

### Key Documentation

- `infra/wiki/context.md` - Working context guidelines
- `infra/wiki/git-workflow.md` - Git branching model
- `infra/wiki/livecycle.md` - Ticket lifecycle

### Git Workflow

1. Ensure `dev` branch is up-to-date and working tree is clean
2. Create feature branch: `feature-<hash>--<human_name>`
3. Work exclusively in the feature branch
4. Upon completion:
   - Run all necessary tests and checks
   - Push feature branch to remote
   - Create Pull Request: `feature-<hash>--<human_name>` → `dev`
   - Switch repositories back to `dev` branch
5. PR rules:
   - Always target `dev`
   - Include links to task PR in all status messages
   - Do not merge PRs manually

### Ticket Lifecycle

Project board columns: `Todo`, `In Progress`, `Clarification`, `Review`

- DevOrc only works with tickets in `Todo` column
- Getting a ticket: Find in `Todo`, move to `In Progress`, load ticket context
- Each iteration executes a piece of work and determines next ticket state
- Completion: Verify checks passed, create PR to `dev`, add PR links to ticket, move to `Review`