# Architectural rules

- Миграции и сиды достижений/уровней — только здесь (`make migrate service=profile`).
- `profile.proto` импортирует `auth.UserIdRequest` — правка `auth.proto` ломает codegen
  profile; перегенерировать оба (`make proto-generate`).
- Консьюмер `user.created` обязан идемпотентно создавать профиль (повторные
  доставки Kafka возможны).
