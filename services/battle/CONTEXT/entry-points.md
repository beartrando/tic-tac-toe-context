# Entry points

- gRPC `:50051` — `Battle` + `Health` (только внутренняя сеть).
- Kafka: produce `battle.matched`; consume `engine.battle_updated`,
  топик терминации (env `MESSAGE_CONSUMER_TOPIC_BATTLE_TERMINATED`).
