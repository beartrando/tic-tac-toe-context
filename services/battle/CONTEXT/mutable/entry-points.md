# Entry points

- gRPC `:50051` — `Battle` (`Get`, `GetMany`, `Init`) + `Health` (только внутренняя сеть).
- Kafka: produce `battle.matched`, `battle.timeout`; consume `engine.battle_updated`,
  топик терминации (env `MESSAGE_CONSUMER_TOPIC_BATTLE_TERMINATED`).
