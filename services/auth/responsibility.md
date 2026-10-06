# Responsibility

- gRPC `Auth` на `:50051`: `AnonymousSignIn`, `RefreshTokens`, `Logout`, `Get`
  (+ общий `Health`).
- Prisma-модели `User`, `Session`, `Device` (БД `auth` через pgbouncer), миграции и seed
  (`make migrate service=auth`, `make prisma-generate`).
- Хеширование bcrypt, подпись JWT (`ACCESS_SECRET`).
- Produce события `user.created` в Kafka — через pg-boss → Kafka-мост
  (`@shared/pg-boss-manager`), не напрямую.
- Use-cases: `application/use-cases/auth/*`, `health/*`.
