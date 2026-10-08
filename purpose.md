# Purpose

## Project Goal

Provide a distributed, real-time tic-tac-toe game platform with microservices architecture, supporting both
human-vs-human and human-vs-AI gameplay patterns. The system must be highly available, horizontally scalable, and
provide consistent game state across all clients.

## Core Objectives

### Gameplay Objectives

1. **Enable real-time battles** between two players via WebSocket streaming
    - Battle initialization through gateway
    - Real-time move synchronization via WebSocket
    - Bot opponent for solo gameplay

2. **Support authentication** for user identification and session management
    - Anonymous sign-in (device ID based)
    - Token refresh workflow
    - User session management
    - Device repository tracking

3. **Maintain player profiles** with ratings, levels, and statistics
    - Profile creation and persistence
    - Rating progression across sessions
    - Level-up logic based on experience
    - Statistics tracking (wins, losses, draws)

4. **Ensure reliable game state** through distributed data stores
    - PostgreSQL as the source of truth (separate DBs per service: matchmaking write, battle read)
    - Redis for real-time battle state caching (engine)
    - Kafka for asynchronous event propagation (matchmaking.matched, battle.updated, battle.terminated)
    - pgbouncer for connection pooling

5. **Provide AI opponent** functionality through the bot service
    - Heuristic-based move generation
    - Battle simulation for AI decision making
    - Timeout handling for AI turns

### User Value

- Players can engage in tic-tac-toe matches asynchronously or in real-time
- Anonymous play is supported without account creation
- Authenticated users have persistent profiles with rating progression
- Bot opponents provide single-player gameplay options
- Real-time updates via WebSocket for responsive gameplay

### Technical Goals

1. **Microservices architecture** for scalability and maintainability
    - Each service has a single responsibility
    - Services can be independently deployed and scaled
    - Clear separation of concerns

2. **gRPC for inter-service communication**
    - Efficient binary protocol
    - Strongly typed contracts via Protocol Buffers
    - All inter-service calls go through gRPC

3. **Protocol Buffers for contract definition**
    - Contract-first approach
    - Definitions in `infra/proto/`
    - Generated code for TypeScript services

4. **Docker containerization** for consistent deployments
    - Each service has its own Dockerfile
    - Docker Compose for local development
    - Kubernetes manifests for production

5. **Kubernetes for orchestration and scaling**
    - Deployment manifests in `infra/k8s/base/`
    - Environment-specific overlays in `infra/k8s/overlays/`
    - Rolling updates and health checks

6. **Message-driven architecture** for async coordination
    - Kafka for battle state changes
    - pg-boss for background job processing
    - Decoupling services through events

7. **Real-time WebSocket communication** for battle events
    - Streaming service handles WebSocket connections
    - Battle state synchronization in real-time
    - Bot connections included in stream

## User Stories

- **Anonymous player**: Signs in with device ID, creates/joins battle, plays in real-time
- **Registered user**: Signs in, has profile with rating, can create/join battles, rating changes based on results
- **AI player**: Plays against bot, bot makes strategic moves, game state updates in real-time
- **Spectator**: Can observe battles via WebSocket (if feature exists)

## Technical Goals (Expanded)

### Architecture Quality Attributes

- **Scalability**: Add more instances of any service without downtime
- **Reliability**: PostgreSQL as source of truth, Redis as cache, Kafka for event durability
- **Maintainability**: Clear service boundaries, shared modules for common concerns
- **Testability**: Colocated tests (.spec.ts), battle timeout tests as exception
- **Observability**: Health checks (readyz/livez/full-health), structured logging (@shared/logger)
- **Performance**: gRPC for internal calls, Redis cache for frequent reads, pgbouncer for connection efficiency

### Security Considerations

- Authentication required for most operations
- Anonymous mode supported for basic gameplay
- Token-based authorization (JWT-like session tokens)
- Device ID tracking for anonymous users
- Rate limiting through pgbouncer and Redis

## Success Metrics

- Battle initialization time < 2 seconds
- Move synchronization latency < 100ms via WebSocket
- 99.9% uptime for game services
- Support for 1000+ concurrent battles
- Rating system accuracy across sessions