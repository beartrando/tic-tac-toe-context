# Entry Points

## API Entry Points

### HTTP/gRPC Gateway

| Entry Point | Path/Method | Description |
|-------------|-------------|-------------|
| `POST /auth/anonymous-sign-in` | Gateway | Anonymous player sign-in |
| `POST /auth/refresh-tokens` | Gateway | Refresh authentication tokens |
| `POST /auth/logout` | Gateway | Revoke tokens |
| `GET /auth/user/:id` | Gateway | Retrieve user info |
| `POST /battle/init` | Gateway | Create new battle |
| `POST /battle/move` | Gateway | Make a move in battle |
| `POST /battle/leave` | Gateway | Leave battle |
| `GET /battle/:id` | Gateway | Get battle state |
| `WS /stream` | Streaming | Real-time battle events |

### gRPC Services

| Service | Method | Proto Definition |
|---------|--------|------------------|
| auth | Logout, RefreshTokens, AnonymousSignIn, Get | `infra/proto/auth.proto` |
| battle | Get, GetMany, Init, Leave | `infra/proto/battle.proto` |
| profile | GetByUser, Get, GetOrCreate | `infra/proto/profile.proto` |
| orchestration | Proxy | `infra/proto/orchestration.proto` |

### WebSocket Events

| Event | Direction | Description |
|-------|-----------|-------------|
| `BattleInit` | Init → Streaming | New battle created |
| `BattleStart` | Init → Streaming | Battle started |
| `BattleMove` | Move → Streaming | Move made |
| `BattleLeave` | Leave → Streaming | Player left |
| `BattleJoinBot` | Bot → Streaming | Bot connected |

### Configuration Entry Points

- `infra/services/*/tsconfig.json` - TypeScript configuration per service
- `infra/pnpm-workspace.yaml` - Monorepo workspace configuration
- `infra/docker-compose.yml.dist` - Local development Docker composition
- `infra/k8s/base/` - Kubernetes base manifests