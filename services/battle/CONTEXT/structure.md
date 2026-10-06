# Structure

- `src/infrastructure/grpc/handlers/battle.handler.ts` — реализация RPC Battle.
- `src/infrastructure/db/prisma/schema.prisma` — `Battle`, `BattleStatus`, `BattleCellValue`.
- `src/infrastructure/message/` — продюсер `battle.matched`, консьюмер топиков engine'а.
- `src/domain/battle/` — `battle.entity.ts`, `battle.repository.ts`, `enums/`, `value-objects/`.
- `src/application/use-cases/battle/` — init/get/leave и обработка Kafka-событий.
