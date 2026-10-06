# Structure

- `src/infrastructure/grpc/handlers/profile.handler.ts` — реализация RPC Profile.
- `src/infrastructure/db/prisma/schema.prisma` — `Profile`, `ProfileAchievement`,
  `Achievement`, `ExperienceLevel`; `repositories/prisma-profile.repository.ts`.
- `src/infrastructure/message/` — консьюмер `user.created`, продюсер `profile.created`.
- `src/domain/profile/` — сущности и value-objects профиля.
- `src/application/use-cases/profile/` — get/getOrCreate и обработка `user.created`.
