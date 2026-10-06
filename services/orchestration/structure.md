# Structure

- `src/infrastructure/proxy/` — `routes/{auth,profile}.routes.ts`,
  `controllers/{auth,profile}-controller.ts`, `middlewares/`, `adapter/` (ResponseAdapter:
  JSON → `ProxyResponse`), `utils/`.
- `src/infrastructure/grpc/clients/` — клиенты auth/profile; `handlers/orchestration.handler.ts`.
- `src/infrastructure/cache/` — Redis-кеш (`cache-store.ts`, `service.ts`, `mappers/`).
- `src/infrastructure/auth/` — проверка JWT для прокси.
- `src/application/use-cases/{auth,profile,health}/`.
