# Entry points

- gRPC `:50051` — `Engine.BattleChannel` + `Health` (только внутренняя сеть).
- Kafka: consume `battle.matched`; produce `engine.battle_updated`,
  `engine.battle_started`, `engine.battle_finished`.
- Клиент потока: [../../streaming/](../../streaming/CONTEXT/purpose.md)
  (`SERVICE_ENGINE_URL=engine:50051`).
