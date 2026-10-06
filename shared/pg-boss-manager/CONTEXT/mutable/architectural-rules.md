# Architectural rules

- События Kafka отправляются **только** через мост (pg-boss → producer), не напрямую —
  это гарантирует ретраи через персистентную очередь.
- Настройки — через env `PG_BOSS_MAX`, `PG_BOSS_MAINTENANCE_INTERVAL_SECONDS`,
  `PG_BOSS_APPLICATION_NAME`; не хардкодить.
