# Structure

- `src/infrastructure/websocket/` — `server.service.ts`, `connection.handler.ts`,
  `handlers/`, `registers/`, `senders/`, `configs/`, `types/`.
- `src/infrastructure/grpc-stream/` — `service.ts`, `channels/engine.stream.ts`,
  `interfaces/` — обёртка над bidi-клиентом engine'а.
- `src/infrastructure/grpc/` — `configs/grpc.config.ts` (карта `SERVICE_*_URL`),
  `handlers/` (`battle.service.ts`, `profile.service.ts` — фактически закомментированы,
  мёртвый код), `health`.
- `src/infrastructure/{state-store,timer}/` — состояние WS-сессий и таймеры на
  `TimerService` (копия из engine): WS-idle (`callbacks/websocket-timeout-callback-factory.ts`)
  и простоя битв (`callbacks/battle-timeout-callback-factory.ts`, env
  `BATTLE_IDLE_TIMEOUT_SECONDS`, `BATTLE_POLL_INTERVAL_SECONDS`).
- `src/application/use-cases/battle/battle-timeout-check.ts` — один цикл опроса battle
  (`GetMany`) о простаивающей битве: терминальный статус → push клиентам → отписка.
- `src/engine/config/` — конфиг подключения к engine; `src/application/use-cases/`.
