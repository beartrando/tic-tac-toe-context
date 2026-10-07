# Dependency Direction

## Service Dependencies

### Internal Service Dependencies

```
gateway → auth, profile, orchestration, battle, streaming
auth → (none - root service)
profile → auth
orchestration → (none - proxy only)
battle → auth, profile
streaming → battle, profile
bot → battle, profile
```

### Shared Module Dependencies

```
shared/logger → (none)
shared/errors → (none)
shared/kafka-manager → (none)
shared/grpc-client-manager → (none)
shared/pg-boss-manager → (none)

services/* → shared/*
```

### Dependency Rules

1. **No circular dependencies** allowed between services
2. **Services depend on shared modules**, not vice versa
3. **Shared modules must remain framework-agnostic**
4. **Proto definitions are the only allowed inter-service contracts**
5. **No direct service imports** - all communication via gRPC

### Data Flow

- **Request/Response**: gateway ↔ services via gRPC
- **Events**: services ↔ Kafka (asynchronous)
- **Cache**: services ↔ Redis (read/write)
- **Persistence**: services ↔ PostgreSQL (via pgbouncer pool)