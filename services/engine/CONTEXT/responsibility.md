# Responsibility

- gRPC `Engine.BattleChannel` (`stream BattleRequest → stream BattleResponse`) на
  `:50051` — bidi-сервер для событий `create/start/move/leave/disconnect/ping`;
  хендлер `engine.handler.ts` + `EngineStreamContainer` (реестр открытых потоков).
- Домен битвы: `domain/battle/` (`battle.entity.ts`) — правила игры и переходы статусов.
- Таймеры: `infrastructure/timer/` — лимит хода (`BATTLE_TURN_TIME_LIMIT`) и лимит
  старта (`BATTLE_START_TIME_LIMIT`).
- Кеш/состояние: `infrastructure/cache/` (Redis, ioredis) и `infrastructure/state-store/`.
- Kafka: consume `battle.matched`; produce `engine.battle_updated`,
  `engine.battle_started`, `engine.battle_finished`.
- Use-cases: `application/use-cases/engine/*`, `health/*`.
