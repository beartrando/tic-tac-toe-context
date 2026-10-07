# Responsibility

## Service Responsibilities

### gateway
- Entry point for all external HTTP and gRPC requests
- Routes requests to appropriate internal services
- Handles request validation and transformation
- Aggregates responses from multiple services when needed

### auth
- Authentication and authorization for all services
- Manages user sessions and tokens
- Handles anonymous sign-in and device identification
- Provides user retrieval by ID

### profile
- User profile management
- Profile creation and retrieval
- Rating and level management

### orchestration
- Generic method routing proxy
- Forwards requests to appropriate services based on method name

### battle
- Tic-tac-toe game logic and state management
- Battle creation and matching
- Move validation and processing
- Win/draw detection and game state tracking

### streaming
- Real-time WebSocket communication
- Battle event broadcasting (init, start, move, leave)
- Bot connection management

### pgbouncer
- PostgreSQL connection pooling
- Connection rate limiting
- Performance optimization for database access

### redis
- Cache storage for frequent read operations
- Pub/sub for distributed events
- Session and state caching

### kafka
- Asynchronous event processing
- Battle state changes propagation
- Decoupling between services

### bot
- AI opponent logic
- Move generation and strategy
- Battle simulation and timeout handling