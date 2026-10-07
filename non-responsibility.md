# Non-Responsibility

## What Services Are NOT Responsible For

### auth Service
- **NOT responsible for**: Game logic, battle state, move validation
- **NOT responsible for**: User profile data (except authentication-related)
- **NOT responsible for**: WebSocket event handling
- **NOT responsible for**: Tic-tac-toe board state

### battle Service
- **NOT responsible for**: Authentication/authorization (handled by auth)
- **NOT responsible for**: User profile management (handled by profile)
- **NOT responsible for**: WebSocket connection management (handled by streaming)
- **NOT responsible for**: Player rating calculations (handled by profile)

### profile Service
- **NOT responsible for**: Authentication tokens (handled by auth)
- **NOT responsible for**: Game state or battle logic (handled by battle)
- **NOT responsible for**: Real-time event broadcasting (handled by streaming)
- **NOT responsible for**: Bot AI behavior (handled by bot)

### streaming Service
- **NOT responsible for**: Game logic or battle state (handled by battle)
- **NOT responsible for**: Authentication (handled by auth)
- **NOT responsible for**: Profile data management (handled by profile)
- **NOT responsible for**: Move validation (handled by battle)

### gateway Service
- **NOT responsible for**: Business logic (handled by individual services)
- **NOT responsible for**: Authentication state management (handled by auth)
- **NOT responsible for**: Game move processing (handled by battle)
- **NOT responsible for**: Profile data retrieval (handled by profile)

### bot Service
- **NOT responsible for**: Authentication or user management
- **NOT responsible for**: Game state persistence (handled by battle)
- **NOT responsible for**: Real-time WebSocket connections beyond bot moves (handled by streaming)
- **NOT responsible for**: Profile data (handled by profile)

### Shared Modules
- **logger**: NOT responsible for business logic or application flow
- **errors**: NOT responsible for domain rules, only for error formatting
- **kafka-manager**: NOT responsible for producing specific events, only message format
- **grpc-client-manager**: NOT responsible for business logic, only gRPC connection management
- **pg-boss-manager**: NOT responsible for query logic, only connection pooling and task management