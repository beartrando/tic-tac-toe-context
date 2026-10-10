# Архитектура

## Общее

- Монорепо `infra`, корневой npm-пакет `game`; pnpm workspaces (`services/*`, `shared/*`) + turborepo.
- turbo: `build` (зависит от `^build`, outputs `dist/**`), `dev` (persistent, parallel).
- Каждый сервис — отдельный git-репозиторий (субмодуль `beartrando/*`), монорепо хранит gitlinks.

## Сервисы

| сервис | репозиторий | зависимости (docker-compose) | порты | ответственность |
|---|---|---|---|---|
| gateway | tic-tac-toe-gateway | — | HTTP 9090, gRPC 50051 | единый вход; HTTP API, прокси, агрегация health |
| auth | tic-tac-toe-auth | kafka, pgbouncer | gRPC 50051 | сессии/токены: Logout, RefreshTokens, AnonymousSignIn, Get |
| profile | tic-tac-toe-profile | kafka, pgbouncer | 50051 | профили: GetByUser, Get, GetOrCreate |
| orchestration | tic-tac-toe-orchestration | kafka, pgbouncer | 50051 | оркестрация сценариев, rpc Proxy |
| battle | tic-tac-toe-battle | kafka, pgbouncer | 50051 | бои: Get, GetMany, Leave |
| engine | tic-tac-toe-engine | kafka, pgbouncer, redis | 50051 | движок игры, bidi-стрим BattleChannel |
| matchmaking | tic-tac-toe-matchmaking | kafka, pgbouncer | 50051 | баттлы: Init (создание/join, матчинг), владеет battles в БД matchmaking (issue #75, выделен из battle) |
| bot | tic-tac-toe-bot | pgbouncer | 50051 | бот-игрок |
| streaming | tic-tac-toe-streaming | — | HTTP 8080 | стриминг данных клиентам |
| front | tic-tac-toe-front | — | — | Flutter-клиент |

## Инфраструктура (docker-compose)

- postgres 17.6 (:5432), edoburu/pgbouncer (пулинг), apache/kafka 4.1.1 (:9092),
  redis 7.2, provectuslabs/kafka-ui.
- Volumes: `postgres_data`, `kafka_data`. Сеть `backend` (bridge).
- Контейнеры: `ttt-<service>`; команда dev: `pnpm dlx turbo run dev --parallel --filter <svc>`;
  монтируются `src/` и `package.json` сервиса.

## Взаимодействие

- gRPC между сервисами (порт 50051), клиенты через `@shared/grpc-client-manager`.
- HTTP наружу — через gateway (:9090); health-агрегация в gateway
  (`src/application/use-cases/health/`).
- Kafka (`@shared/kafka-manager`) — события между сервисами.
- pg-boss (`@shared/pg-boss-manager`) — очереди/фоновые задачи в postgres.
- Redis — используется engine.
- `@shared/state-store`, `@shared/cache-store` — состояние/кэш.

## Шаблон сервиса (Node/TS)

```
src/
├── app.ts
├── application/use-cases/      # бизнес-логика
├── composition/build-application.ts
├── contracts/proto/            # СГЕНЕРИРОВАНО из proto/ (не редактировать)
└── infrastructure/
    ├── grpc/{handlers,clients,config,mappers,service}
    ├── http/{controllers,routes,middlewares,server.service}
    ├── db/                     # prisma-схемы и миграции
    └── job/                    # pg-boss задачи
```

- Тесты: jest, `*.spec.ts` рядом с кодом (`pnpm test`).
- Prisma-сервисы: auth, battle, bot, engine, orchestration, profile, matchmaking, streaming.

## shared-модули (`shared/*`, все — субмодули beartrando/*)

`cache-store`, `errors`, `grpc-client-manager`, `kafka-manager`, `logger`,
`pg-boss-manager`, `state-store`, `timer`.
В корневом `pnpm-workspace.yaml` все `@shared/*` зафиксированы как `workspace:*`
(override'ы внутри субмодулей игнорируются).

## proto-контракты (`proto/`, репозиторий tic-tac-toe-proto)

`auth.proto`, `battle.proto`, `engine.proto`, `matchmaking.proto`, `orchestration.proto`, `profile.proto`,
`streaming.proto`, `common/{empty,ping,health,error,response,key_value}.proto`.
Генерация: `make proto-generate` (ts-proto для Node в `src/contracts/proto`, dart для front).
