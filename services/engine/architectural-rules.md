# Architectural rules

- События потока — oneof в `engine.proto`/`streaming.proto`: добавление типа события =
  правка обоих `.proto` (разные пакеты!) + синхронная правка мапперов в engine и streaming.
- Изменение таймеров — только через env (`BATTLE_*_TIME_LIMIT`), не хардкод.
- Prisma-датасource существует исключительно под pg-boss; доменные модели в engine
  не заводить (реестр битв — в [../../battle/](../../battle/CONTEXT/purpose.md)).
