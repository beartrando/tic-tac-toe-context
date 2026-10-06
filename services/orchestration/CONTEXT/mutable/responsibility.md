# Responsibility

- gRPC `Orchestration.Proxy(ProxyRequest) → ProxyResponse` на `:50051` (+ `Health`).
- Свой HTTP-роутер поверх прокси: `/auth/{anonymousSignIn,getMe,refreshTokens,verify}`,
  `/profile/{getMe,get}` — маршруты → контроллеры → gRPC-клиенты auth/profile
  (через `@shared/grpc-client-manager`).
- Транспортная авторизация: проверка JWT (`ACCESS_SECRET`) в middleware прокси.
- Кеш в Redis (`infrastructure/cache/`, ioredis).
- Prisma/pg-boss-датасource без доменных моделей (нужен очередям pg-boss);
  use-cases: `auth`, `profile`, `health`.
