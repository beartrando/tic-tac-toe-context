# Non-Responsibility

## What Services Are NOT Responsible For (Expanded)

### auth Service

- **NOT responsible for**:
  - Game logic, battle state, move validation
  - User profile data (except authentication-related fields like user_id)
  - WebSocket event handling
  - Tic-tac-toe board state
  - AI opponent logic
  - Rating calculations (handled by profile service)
  - Battle creation or matching

### battle Service

- **NOT responsible for**:
  - Authentication/authorization (handled by auth service)
  - User profile management (handled by profile service)
  - WebSocket connection management (handled by streaming service)
  - Player rating calculations (handled by profile)
  - AI opponent move generation (handled by bot)
  - Engine domain rules (handled by engine service)
  - User authentication state
  - Profile nickname or level management

### engine Service

- **NOT responsible for**:
  - Database persistence (handled by battle's Prisma repositories)
  - gRPC API contracts (handled by battle.proto)
  - WebSocket communication (handled by streaming)
  - User authentication (handled by auth)
  - Profile data retrieval (handled by profile)
  - Battle state storage (handled by battle service with Prisma)
  - Move validation at API level (handled by battle domain)

### profile Service

- **NOT responsible for**:
  - Authentication tokens (handled by auth service)
  - Game state or battle logic (handled by battle + engine)
  - Real-time event broadcasting (handled by streaming)
  - Move validation (handled by battle)
  - Bot AI behavior (handled by bot)
  - Authentication session management
  - Device ID tracking (handled by auth)

### streaming Service

- **NOT responsible for**:
  - Game logic or battle state (handled by battle + engine)
  - Authentication (handled by auth service)
  - Profile data management (handled by profile service)
  - Move validation (handled by battle)
  - WebSocket connection establishment beyond battle events
  - Bot AI logic (handled by bot)
  - Battle creation or initialization

### gateway Service

- **NOT responsible for**:
  - Business logic (handled by individual services)
  - Authentication state management (handled by auth)
  - Game move processing (handled by battle)
  - Profile data retrieval (handled by profile)
  - Token generation (handled by auth)
  - Game rule enforcement
  - Battle state persistence

### bot Service

- **NOT responsible for**:
  - Authentication or user management
  - Game state persistence (handled by battle)
  - Real-time WebSocket connections beyond bot moves (handled by streaming)
  - Profile data (handled by profile)
  - Battle creation (handled by battle)
  - Move validation at domain level (handled by engine)
  - User session tracking

### Shared Modules

#### @shared/logger
- **NOT responsible for**:
  - Business logic or application flow
  - Control flow decisions
  - Domain rules
  - Test execution orchestration

#### @shared/errors
- **NOT responsible for**:
  - Domain rules or business logic
  - Application flow control
  - Test setup or teardown
  - Error handling strategy (only format/types)

#### @shared/kafka-manager
- **NOT responsible for**:
  - Producing specific event content (only message format/routing)
  - Event business logic
  - Synchronous request-response patterns
  - Consumer business logic beyond message format

#### @shared/grpc-client-manager
- **NOT responsible for**:
  - Business logic or application flow
  - gRPC connection business logic beyond connection management
  - Service discovery or address management
  - Request/response content processing

#### @shared/pg-boss-manager
- **NOT responsible for**:
  - Query logic or database operations
  - Business logic within tasks
  - Query construction or execution
  - Data modeling or schema definition

### Cross-Service Boundary Violations

#### Common Anti-Patterns

1. **Direct service imports**
   - ❌ `import { ProfileService } from '../profile/service'`
   - ✅ Use gRPC calls through generated clients

2. **Hardcoded service addresses**
   - ❌ `grpc.loadPackageDefinition(...)` with hardcoded ports
   - ✅ Use environment variables or config

3. **Bypassing gateway**
   - ❌ External clients calling services directly
   - ✅ All external requests go through gateway

4. **Storing context in multiple locations**
   - ❌ CONTEXT/ directories in service projects
   - ✅ All context in `infra/context/` only

5. **Skipping authentication**
   - ❌ Calls to battle/profile without valid token
   - ✅ All gateway endpoints validate auth tokens first

6. **Direct database queries**
   - ❌ Raw SQL or Prisma queries bypassing service layer
   - ✅ All data access through service use cases

7. **Bypassing Kafka**
   - ❌ Direct event publishing without kafka-manager
   - ✅ All async events go through @shared/kafka-manager

8. **Skipping pgbouncer**
   - ❌ Direct PostgreSQL connections without pooling
   - ✅ All DB access through services using pgbouncer

### Service Boundary Verification

#### How to Verify Boundaries

1. **Import graph analysis**
   - Check for forbidden `import` statements between services
   - Use `grep` for cross-service imports
   - Run CI import validation

2. **Responsibility checklist**
   - Before adding new feature, verify it belongs to correct service
   - Use non-responsibility.md as reference
   - Review with team during PR

3. **Architectural rules validation**
   - Run `pnpm build` to check proto compilation
   - Run `pnpm test` to verify test conventions
   - Review architectural-rules.md compliance

4. **Context maintenance**
   - New knowledge → Context (if working context)
   - New knowledge → Issue (if task-specific)
   - New knowledge → Wiki (if long-term architectural)

### Boundary Enforcement

#### CI/CD Checks
- Import graph analysis
- Proto schema validation
- TypeScript type checking
- Test convention enforcement (`.spec.ts` vs `.test.ts`)

#### Code Review
- PR reviewer checks service boundary compliance
- Architectural rules review
- Context update appropriateness
- Dependency direction validation

#### Manual Review Process
1. Developer verifies new feature belongs to correct service
2. Reviewer checks import graph
3. Reviewer verifies responsibility boundaries
4. Context updated if needed
5. PR merged to `dev`

### Exception Handling

#### Battle/Streaming Test Exception (commit 963c3df)
- Rule: "update architectural rules: .test.ts -> .spec.ts and tsconfig.test.json -> tsconfig.spec.json"
- Battle timeout tests (`battle-timeout-sweep.test.ts`) are intentionally colocated in `src/`
- This is the only exception to the colocated test rule
- All other tests must follow `.spec.ts` convention
- Migration in progress: `tsconfig.test.json` → `tsconfig.spec.json`

#### New Service Addition
- When adding new service, update:
  - `infra/pnpm-workspace.yaml` packages list
  - `infra/services/*/tsconfig.json` (if new)
  - Context repository with new service documentation
  - Architectural rules if boundaries change

#### Emergency Exceptions
- Documented in Issue if temporary
- Time-boxed exception period
- Cleanup required before PR merge
- Architectural rules update if exception becomes permanent