# Parent

## Tic-Tac-Toe Project

Distributed tic-tac-toe game built with a microservices architecture using gRPC for inter-service communication. The
project uses a monorepo structure managed with pnpm.

**Architecture**: Write/Read model separation (CQRS-like) for battle domain:

- **matchmaking** (write model) — battle creation, matchmaking, join, publishes `matchmaking.matched`
- **battle** (read model) — Get, GetMany, TimeoutSweep, consumes `battle.updated`/`battle.terminated`

### Related Context

- [Structure](structure.md) - Project structure and service overview
- [Responsibility](responsibility.md) - Service responsibilities
- [Purpose](purpose.md) - Project purpose and goals
- [Git Workflow](git-workflow.md) - Git branching model and PR process
- [Ticket Lifecycle](livecycle.md) - Ticket workflow and project board columns