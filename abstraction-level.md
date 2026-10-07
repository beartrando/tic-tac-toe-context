# Abstraction Level

## Project Abstraction Level

This project operates at the **microservices architecture** level with **gRPC** as the primary inter-service communication protocol.

### Layers

1. **Presentation Layer** (gateway)
   - HTTP entry point for external clients
   - Request routing and proxying
   - Basic validation and transformation

2. **Business Logic Layer** (services)
   - auth: Authentication and authorization
   - profile: User profile management
   - battle: Tic-tac-toe game logic
   - streaming: Real-time event communication
   - orchestration: Method routing

3. **Data Access Layer**
   - PostgreSQL (primary data store)
   - Redis (cache and pub/sub)
   - Kafka (asynchronous event streaming)

4. **Infrastructure Layer**
   - Docker containerization
   - Kubernetes deployment manifests
   - Connection pooling (pgbouncer)

### Boundary Context

The project boundary encompasses:
- All microservices (gateway, auth, profile, orchestration, battle, streaming, bot)
- Shared modules (logger, errors, kafka-manager, grpc-client-manager, pg-boss-manager)
- Infrastructure services (pgbouncer, redis, kafka, postgres, pgbouncer)
- Git workflow and CI/CD processes

### Integration Points

- gRPC services defined in `infra/proto/`
- REST via gateway proxy
- WebSocket via streaming service
- Message passing via Kafka
- Data queries via PostgreSQL/Redis