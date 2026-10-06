# Non-responsibility

- Ни бизнес-логики, ни БД, ни Kafka, ни pg-boss, ни доменного слоя — в gateway их нет
  (`pg-boss` в package.json объявлен, но не используется).
- Маршрутизации внутренних вызовов и транспортной авторизации — это
  [../../orchestration/](../../orchestration/CONTEXT/purpose.md).
- Определения RPC (→ [../../../proto/](../../../proto/CONTEXT/purpose.md)).
