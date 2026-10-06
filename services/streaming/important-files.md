# Important files

- `src/infrastructure/websocket/server.service.ts` — WS-сервер.
- `src/infrastructure/grpc-stream/channels/engine.stream.ts` — канал в engine.
- `src/infrastructure/timer/` — engine-копия `TimerService` + фабрики колбэков:
  WS-idle и простоя битв (`callbacks/battle-timeout-callback-factory.ts`).
- `src/application/use-cases/battle/battle-timeout-check.ts` — проверка битвы через
  `GetMany` после простоя (push + отписка на терминальный статус).
- `src/infrastructure/grpc/clients/battle.client.ts` — gRPC-клиент battle (`init`, `getMany`).
- `.env.dist` — `WEBSOCKET_PORT=8080`, `SERVICE_{AUTH,PROFILE,ENGINE,BATTLE}_URL`,
  `IDDLE_WEBSOCKET_TIME_LIMIT`, `BATTLE_IDLE_TIMEOUT_SECONDS`, `BATTLE_POLL_INTERVAL_SECONDS`.
