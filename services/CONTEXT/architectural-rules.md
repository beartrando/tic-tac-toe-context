# Architectural rules

- Новый use-case → `application/use-cases/<area>/`; регистрация — в
  `composition/build-application.ts` и в gRPC-хендлере/контроллере.
- Новый репозиторий → интерфейс в `domain/<area>/`, реализация в
  `infrastructure/db/repositories/`, маппер в `infrastructure/db/mappers/`.
- Новый RPC → правка `.proto` в `proto/` (отдельный submodule!) → `make proto-generate`
  → хендлер в `infrastructure/grpc/handlers/`.
- Отправка событий в Kafka — только через pg-boss → Kafka-мост (`@shared/pg-boss-manager`),
  не напрямую через producer.
- Тесты — **исключительно коллокейтед**: `*.spec.ts` рядом с тестируемым кодом,
  отдельные тестовые деревья не используются. Единственное исключение —
  [../bot/](../bot/CONTEXT/purpose.md): его тесты вынесены из дерева исходников.
  Прогон — `make test` в контейнерах.
- Compose монтирует в dev-контейнер `src`, `package.json` и `../../shared` — правки кода
  подхватываются volume-ами без пересборки образа. Пересборка (`docker compose build`)
  не выполняется без явного указания пользователя.
