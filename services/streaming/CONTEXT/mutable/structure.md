# Structure

- `src/infrastructure/websocket/` — `server.service.ts`, `connection.handler.ts`,
  `handlers/`, `registers/`, `senders/`, `configs/`, `types/`.
- `src/infrastructure/grpc-stream/` — `service.ts`, `channels/engine.stream.ts`,
  `interfaces/` — обёртка над bidi-клиентом engine'а.
- `src/infrastructure/grpc/` — `configs/grpc.config.ts` (карта `SERVICE_*_URL`),
  `handlers/` (`battle.service.ts`, `profile.service.ts` — фактически закомментированы,
  мёртвый код), `health`.
- `src/infrastructure/{state-store,timer}/` — состояние WS-сессий и таймеры.
- `src/infrastructure/battle-timeout/` — трекер активности по битвам + периодический
  опрос battle о простаивающих битвах (`BATTLE_IDLE_TIMEOUT_SECONDS`,
  `BATTLE_POLL_INTERVAL_SECONDS`).
- `src/engine/config/` — конфиг подключения к engine; `src/application/use-cases/`.
