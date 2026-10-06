# Structure

- `src/infrastructure/grpc/handlers/battle.handler.ts` — реализация RPC Battle.
- `src/infrastructure/db/prisma/schema.prisma` — `Battle`, `BattleStatus`, `BattleCellValue`.
- `src/infrastructure/message/` — продюсеры `battle.matched` / `battle.timeout`,
  консьюмер топиков engine'а.
- `src/infrastructure/timeout/` — периодический sweep: `service.ts` (интервал),
  `config.ts` (`BATTLE_STATUS_TIMEOUT_SECONDS`, `BATTLE_TIMEOUT_SWEEP_SECONDS`).
- `src/domain/battle/` — `battle.entity.ts` (`timeout()`, `final()`), `battle.repository.ts`
  (`findStale`, `findByIds`), `enums/`, `value-objects/`.
- `src/application/use-cases/battle/` — init/get/get-many/leave, `battle-timeout-sweep`
  и обработка Kafka-событий.
