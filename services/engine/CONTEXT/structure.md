# Structure

- `src/infrastructure/grpc/handlers/engine.handler.ts` — разбор oneof-событий потока.
- `src/infrastructure/grpc/streams/` (`engine.stream.container.ts`) — реестр подключений.
- `src/infrastructure/battle/service.ts` — оркестрация create/start/move/leave.
- `src/infrastructure/timer/` — `service.ts`, `callbacks/`, `callback-factory-holder.ts`.
- `src/infrastructure/cache/` + `src/infrastructure/state-store/` — Redis-состояние битв.
- `src/domain/battle/` — игровая сущность; `src/application/use-cases/engine/`.
