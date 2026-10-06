# Important files

- `src/infrastructure/db/prisma/schema.prisma` — `Battle` + игровые enum'ы.
- `src/infrastructure/grpc/handlers/battle.handler.ts` — gRPC-поверхность (`get`,
  `getMany`, `init`).
- `src/infrastructure/timeout/{service.ts,config.ts}` — sweep таймаута статуса.
- `src/infrastructure/db/prisma/migrations/` — миграции (в т.ч. `ADD VALUE 'TimeOut'`).
- `.env.dist` — топики `battle.matched`, `battle.timeout`, `engine.battle_updated`,
  `engine.battle_terminated` + `BATTLE_STATUS_TIMEOUT_SECONDS`, `BATTLE_TIMEOUT_SWEEP_SECONDS`.
