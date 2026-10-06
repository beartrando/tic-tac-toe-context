# Architectural rules

- Новый use-case → `application/use-cases/<area>/`; регистрация — в
  `composition/build-application.ts` и в gRPC-хендлере/контроллере.
- Новый репозиторий → интерфейс в `domain/<area>/`, реализация в
  `infrastructure/db/repositories/`, маппер в `infrastructure/db/mappers/`.
- Новый RPC → правка `.proto` в `proto/` (отдельный submodule!) → `make proto-generate`
  → хендлер в `infrastructure/grpc/handlers/`.
- Отправка событий в Kafka — только через pg-boss → Kafka-мост (`@shared/pg-boss-manager`),
  не напрямую через producer.
- Тесты: jest с `testMatch: ['**/__tests__/**/*.test.ts']`; фактически тесты есть только
  в `battle`, `bot`, `engine` (остальные `__tests__` содержат `.keep`). Прогон —
  `make test` в контейнерах.
- Compose монтирует в dev-контейнер `src`, `package.json` и `../../shared` — правки кода
  подхватываются volume-ами без пересборки образа. Пересборка (`docker compose build`)
  не выполняется без явного указания пользователя.
