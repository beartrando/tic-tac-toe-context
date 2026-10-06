# Dependency direction

- **Сервисы не импортируют друг друга.** Связь между ними только через протоколы:
  gRPC (по `SERVICE_*_URL`), Kafka (топики), WebSocket, HTTP (bot → gateway).
- Сервисы → `@shared/*`. Обратного направления не существует: shared не знает о сервисах.
- Сервисы → `proto/` строго на этапа генерации кода, не рантайм-зависимость.

## Шаблон Node-сервиса (общий для всех восьми)

Точка входа и wiring — везде одинаковы:

```
src/app.ts → src/composition/build-application.ts → src/application/application.ts
```

`app.ts` импортирует **только** `composition/`. `Application` стартует сервисы в порядке:
message (Kafka) → job (pg-boss) → db (Prisma) → grpc (+ websocket/grpc-stream в streaming,
http/websocket-клиенты в bot), graceful shutdown по SIGINT/SIGTERM.

Слои:

| Слой | Содержимое |
|---|---|
| `contracts/` | только сгенерированный ts-proto код (`proto/*`, `common/*`) |
| `domain/` | сущности, value-objects, интерфейсы репозиториев; не импортирует ничего кроме domain и node |
| `application/` | `application.ts` (lifecycle), `use-cases/*`, `ports/*` |
| `infrastructure/` | адаптеры: `grpc/` (сервер, хендлеры, клиенты, мапперы), `http/`, `db/` (Prisma), `auth/`, `message/` (Kafka), `job/` (pg-boss), плюс уникальные подсистемы сервиса (`websocket/`, `state-store/`, `timer/`, `cache/`, `proxy/`, `grpc-stream/`) |
| `composition/` | единственная точка DI: собирает сервисы, репозитории, use-cases, хендлеры |
| `utils/` | мелкие помощники |

**Фактическое направление зависимостей** (по шаблону проекта):

- `domain` → только `domain` (+ node). Чистый.
- `application` → `domain`, `contracts`, **и конкретные классы `infrastructure`**
  (use-cases импортируют `DbService`, `JobService`, `MessageService`, Prisma-репозитории,
  gRPC-клиенты, proto-мапперы).
- `infrastructure` → `application` (gRPC/HTTP-хендлеры и Kafka-консьюмеры вызывают use-cases),
  `domain` (репозитории `implements` доменные интерфейсы), `contracts`.
- ⇒ связь `application ↔ infrastructure` **взаимная** — это фактическое состояние.
- `composition` → `application` + `infrastructure`; импортируется только из `app.ts`.
- Dependency inversion фрагментарна: порты `application/ports/*` реализуются в
  `infrastructure/auth/service.ts`; интерфейсы репозиториев объявлены в `domain/`.

## Межсервисная топология (фактическая)

- `front` → HTTP `gateway:9090` и WebSocket `streaming:8080`.
- `gateway` → gRPC `orchestration.Proxy` (прокси всего HTTP-трафика); плюс health-фан-аут
  по `SERVICE_*_URL` ко всем семи сервисам (bot, auth, profile, orchestration, engine,
  battle, streaming).
- `orchestration` → gRPC клиенты `auth` и `profile` (через `@shared/grpc-client-manager`);
  маршруты `/auth/*` (`anonymousSignIn`, `getMe`, `refreshTokens`, `verify`) и
  `/profile/*` (`getMe`, `get`); JWT-проверка, кеш в Redis.
- `streaming` → gRPC клиенты `auth`, `profile`, `engine`, `battle` (`SERVICE_*_URL`) и
  bidi-поток `Engine.BattleChannel` (`grpc-stream/channels/engine.stream.ts`) — форвардит
  события битвы (create/start/move/leave/ping) от клиентов в engine.
- `bot` → HTTP `gateway` (`HTTP_GATEWAY`) и WebSocket `streaming` (`WEBSOCKET_STREAMING`);
  включается по Kafka-событию `bot.connecting-request`.
- `battle` ↔ `engine` через Kafka: battle produce `battle.matched` → engine; engine produce
  `engine.battle_updated`, `engine.battle_started`, `engine.battle_finished` → battle
  (в `.env.dist` battle ждёт `engine.battle_terminated` — рассинхрон топика с
  `engine.battle_finished`, фактическое состояние).
- Kafka: `auth` produce `user.created` → `profile` consume; `profile` produce
  `profile.created`. Отправка в Kafka везде идёт через pg-boss → Kafka-мост
  (`@shared/pg-boss-manager`), не напрямую.
- Каждый сервис регистрирует gRPC `Health` (`proto/common/health.proto`) на `:50051`.
