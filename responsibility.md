# Responsibility

## Service Responsibilities (Expanded)

### gateway

- **Responsible for**:
    - External HTTP entry point for all client requests
    - Request routing and forwarding to internal gRPC services
    - Proxy orchestration calls via `Orchestration.Proxy` method
    - Health checks (readyz, livez, full-health)
    - Request logging via @shared/logger
    - Aggregating responses from multiple services when needed
    - HTTP gateway configuration (CORS, body parsing, etc.)
- **NOT responsible for**:
    - Business logic (handled by individual services)
    - Authentication state management (handled by auth service)
    - Game move processing (handled by battle service)
    - Profile data retrieval (handled by profile service)

### auth

- **Responsible for**:
    - Authentication and authorization for all services
    - Managing user sessions and access tokens
    - Anonymous sign-in via device ID
    - Token refresh workflow
    - User retrieval by ID
    - Device repository management
    - Session entity management
    - Passwordless authentication flow
- **NOT responsible for**:
    - Game logic or battle state (handled by battle + engine)
    - User profile data beyond auth-related fields (handled by profile)
    - WebSocket event handling (handled by streaming)
    - Tic-tac-toe board state

### profile

- **Responsible for**:
    - User profile CRUD operations
    - Rating and level management
    - Experience point tracking
    - Nickname management
    - Profile retrieval by user ID or profile ID
    - GetOrCreate workflow
    - Profile association with authenticated users
- **NOT responsible for**:
    - Authentication tokens (handled by auth)
    - Game state or battle logic (handled by battle + engine)
    - Real-time event broadcasting (handled by streaming)
    - Bot AI behavior (handled by bot)

### orchestration

- **Responsible for**:
    - Generic method routing proxy
    - Forwards requests to appropriate services based on method name
    - Proxy controller for gateway integration
    - Message production for cross-service events
    - Health check orchestration
- **NOT responsible for**:
    - Business logic execution (just routes)
    - Authentication (delegates to auth)
    - Game state management (delegates to battle)
    - Profile data (delegates to profile)

### battle

- **Responsible for**:
    - Battle history/read model: Get, GetMany retrieval
    - Battle timeout sweep (admin operation)
    - Battle termination (cancel/timeout)
    - Consuming Kafka events: `battle.updated`, `battle.terminated`
    - Maintaining read-only battle state in its own database
- **NOT responsible for**:
    - Battle creation and matching (handled by matchmaking)
    - Move validation and processing (handled by engine)
    - Win/draw detection (handled by engine)
    - Real-time WebSocket events (handled by streaming)
    - AI opponent logic (handled by bot)
    - Engine domain rules (handled by engine)

### matchmaking

- **Responsible for**:
    - Battle creation and initialization
    - Matchmaking logic: find existing battle, join, or create new
    - Publishing `matchmaking.matched` Kafka event when battle is matched
    - Battle join logic
    - Managing write-model battle state in its own database
- **NOT responsible for**:
    - Battle history/read operations (handled by battle)
    - Move validation and processing (handled by engine)
    - Win/draw detection (handled by engine)
    - Battle timeout sweep (handled by battle)
    - Real-time WebSocket events (handled by streaming)
    - AI opponent logic (handled by bot)
    - Engine domain rules (handled by engine)

### engine

- **Responsible for**:
    - Domain-level battle logic
    - Battle domain model (BattleDomain, BattleEntity)
    - Battle initialization and domain rules
    - Move execution at domain level
    - State store integration (Redis cached state)
    - Battle domain events
    - Publishing Kafka events: `battle.updated`, `battle.terminated` (after moves, timeouts, finishes)
- **NOT responsible for**:
    - Database persistence (handled by battle's Prisma repo for read model)
    - gRPC API contracts (handled by battle.proto / matchmaking.proto)
    - WebSocket communication (handled by streaming)
    - User authentication (handled by auth)
    - Battle creation/matching (handled by matchmaking)

### streaming

- **Responsible for**:
    - WebSocket real-time communication
    - Battle event broadcasting (init, start, move, leave)
    - Bot connection management
    - WebSocket connection lifecycle
    - Battle stream subscription management
    - Real-time battle state synchronization
    - **Before match**: calls `matchmaking.Init()` for battle creation/join
    - **After match**: calls `battle.Get()` / `battle.GetMany()` for battle state
- **NOT responsible for**:
    - Game logic or battle state (handled by engine + battle read model)
    - Authentication (handled by auth)
    - Profile data management (handled by profile)
    - Move validation (handled by engine)
    - Battle creation/matching (handled by matchmaking)
    - Battle history/read operations (handled by battle)

### bot

- **Responsible for**:
    - AI opponent logic and move generation
    - Battle simulation and timeout handling
    - Bot connection to streaming service
    - Making moves on behalf of AI players
    - Bot strategy and difficulty levels
- **NOT responsible for**:
    - Authentication or user management
    - Game state persistence (handled by battle)
    - Real-time WebSocket connections beyond bot moves
    - Profile data (handled by profile)

### pgbouncer

- **Responsible for**:
    - PostgreSQL connection pooling
    - Connection rate limiting
    - Performance optimization for database access
    - Managing connections between services and PostgreSQL
- **NOT responsible for**:
    - Query logic (handled by services via Prisma)
    - Data modeling (handled by Prisma schemas)
    - Authentication (handled by auth service)

### redis

- **Responsible for**:
    - Cache storage for frequent read operations
    - Pub/sub for distributed events
    - Session and state caching
    - Battle state caching for fast access
    - Rate limiting data
- **NOT responsible for**:
    - Primary data storage (PostgreSQL is the source of truth)
    - Business logic or query execution

### kafka

- **Responsible for**:
    - Asynchronous event processing
    - Battle state changes propagation (battle updated, battle terminated)
    - User created events
    - Decoupling between services
    - Message format and routing
- **NOT responsible for**:
    - Business logic or event handling content (just message format/routing)
    - Synchronous request-response patterns (use gRPC)
