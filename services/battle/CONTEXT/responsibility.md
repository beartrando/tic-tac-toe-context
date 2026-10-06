# Responsibility

- gRPC `Battle` на `:50051`: `Get`, `Init`, `Leave` (+ `Health`).
- Prisma-модель `Battle` (enum `BattleStatus`, `BattleCellValue` — поле «крестик-нолик»),
  миграции и seed.
- Produce `battle.matched` (матчинг найден) → consume engine'овских
  `engine.battle_updated` / терминации битвы.
- Use-cases: `application/use-cases/battle/*`, `health/*`.
