# Architectural Rules

## General Rules

1. **Single Source of Truth**: Project context is stored exclusively in the `context/` repository. CONTEXT/ directories within service projects have been removed (see commit 38b9ee6).

2. **gRPC Contract First**: All inter-service communication must use Protocol Buffers defined in `infra/proto/`. Services must not directly import each other's code.

3. **Monorepo Structure**: All services reside in the pnpm monorepo under `infra/services/`. Shared modules are under `infra/shared/`.

4. **Feature Branch Workflow**: All development must occur in feature branches named `feature-<hash>--<human_name>`, targeting `dev` via Pull Request.

5. **Ticket Context**: Each task must have an associated ticket. Context updates must relate to the current task or be reflected in Wiki for long-term value.

6. **Service Responsibility Boundaries**:
   - **auth**: Authentication, authorization, tokens, sessions
   - **profile**: User profiles, ratings, levels, statistics
   - **battle**: Game state, moves, matchmaking, win/draw detection
   - **streaming**: WebSocket events, battle real-time updates
   - **orchestration**: Method routing, proxy functionality
   - **gateway**: External API entry point, request routing
   - **bot**: AI opponent logic, move generation
   - **shared/***: Cross-cutting concerns (logger, errors, kafka-manager, grpc-client-manager, pg-boss-manager)

7. **Dependency Direction**: Dependencies should flow from more specific to more generic modules. Shared modules should not depend on service-specific code.

8. **Testing**: Tests are colocated with source code (`.spec.ts` files alongside `.ts` implementation files). Battle/streaming tests are the exception as colocated in `src`.

9. **Containerization**: All services must be Dockerizable. Dockerfiles exist in each service directory.

10. **Kubernetes Deployment**: All services have Kubernetes manifests in `infra/k8s/base/` and overlays in `infra/k8s/overlays/`.

## Prohibited Patterns

- Direct service-to-service imports (use gRPC instead)
- Hardcoded service addresses (use service discovery or environment variables)
- Bypassing the gateway for external access
- Storing context in multiple locations (context/ is the single source of truth)