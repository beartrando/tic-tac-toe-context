# Responsibility

- `PgBossManager` (singleton, экспорт по умолчанию) — инициализация pg-boss, постановка
  и выполнение задач; конфиг — env `PG_BOSS_*` (max, maintenance interval, application name).
- `internal/queue-manager.ts` — очереди; `internal/worker-manager.ts` — воркеры.
- `internal/kafka-bridge.ts` — мост pg-boss → Kafka (`createProducer` из
  [../../kafka-manager/](../../kafka-manager/CONTEXT/purpose.md)) и обратно
  (Kafka → pg-boss-задача, например `bot.connecting-request` в боте).
- `internal/interfaces.ts`, `internal/types.ts`, `internal/utils.ts`, `src/types.ts`.
