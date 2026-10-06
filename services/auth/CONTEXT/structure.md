# Structure

- `src/infrastructure/grpc/handlers/auth.handler.ts` — реализация RPC Auth.
- `src/infrastructure/db/` — `prisma/schema.prisma` (`User`, `Session`, `Device`),
  `repositories/prisma-{user,session,device}.repository.ts`, `mappers/`.
- `src/infrastructure/auth/service.ts` — токен-сервис, реализует порт
  `application/ports/token-service.port.ts`.
- `src/infrastructure/message/` — Kafka-продюсер `user.created` (конфиг топика — env).
- `src/domain/{user,session,device}/` — сущности, value-objects, интерфейсы репозиториев.
- `src/application/use-cases/auth/` — вход/рефреш/выход; `health/` — health-хендлеры.
