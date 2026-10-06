# Architectural rules

- Фактический рассинхрон топиков: engine produce `engine.battle_finished`
  (`MESSAGE_PRODUCER_TOPIC_BATTLE_TERMINATED`), а battle consume
  `engine.battle_terminated` — до выравнивания значений консьюмер engine-событий
  не получает терминации. Любая правка топиков — согласованно в обоих `.env.dist`.
- Миграции `Battle`-модели — только здесь (`make migrate service=battle`).
