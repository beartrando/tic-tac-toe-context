# Responsibility

- gRPC `Profile` на `:50051`: `GetByUser`, `Get`, `GetOrCreate` (+ `Health`).
- Prisma-модели `Profile`, `ProfileAchievement`, `Achievement`, `ExperienceLevel`
  (БД `profile`), миграции и seed.
- Consume `user.created` → создать профиль → produce `profile.created`
  (Kafka идёт через pg-boss → Kafka-мост).
- Use-cases: `application/use-cases/profile/*`, `health/*`.
