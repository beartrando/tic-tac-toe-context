# Responsibility

- gRPC `Battle` на `:50051`: `Get`, `GetMany` (битвы по списку id — для опроса
  [../../streaming/](../../streaming/CONTEXT/purpose.md)), `Init`, `Leave` (+ `Health`).
- Prisma-модель `Battle` (enum `BattleStatus` — включая `TimeOut`, `BattleCellValue` — поле
  «крестик-нолик»), миграции и seed.
- Produce `battle.matched` (матчинг найден) и `battle.timeout` (битва переведена в `TimeOut`)
  → consume engine'овских `engine.battle_updated` / терминации битвы.
- **Таймаут статуса** (`infrastructure/timer/`, use-case `battle-timeout-sweep`): sweep
  каждые `BATTLE_TIMEOUT_SWEEP_SECONDS` (10) переводит в `TimeOut` битвы, чей статус не
  менялся дольше `BATTLE_STATUS_TIMEOUT_SECONDS` (180) — по полю `updated_at`.
  Периодичность даёт engine-ский `TimerService` (`callbacks/battle-timeout-callback-factory.ts`
  перевооружает таймер после каждого sweep), а не `setInterval`.
- Use-cases: `application/use-cases/battle/*`, `health/*`.
