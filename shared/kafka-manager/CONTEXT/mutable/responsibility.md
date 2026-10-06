# Responsibility

- `producer.ts` — отправка сообщений; `consumer.ts` — чтение; `admin.ts` — операции с
  топиками (создание); `register.ts` — регистрация топиков/хендлеров.
- `types.ts` — общие типы; конфиг по умолчанию — `kafka:9092` (env `KAFKA_BROKERS`,
  `KAFKA_CLIENT_ID`).
- Используется сервисами напрямую и мостом `pg-boss-manager` (KafkaBridge → createProducer).
