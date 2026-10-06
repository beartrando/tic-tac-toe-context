# Important files

- `src/infrastructure/websocket/server.service.ts` — WS-сервер.
- `src/infrastructure/grpc-stream/channels/engine.stream.ts` — канал в engine.
- `src/infrastructure/battle-timeout/{service.ts,battle-activity.tracker.ts,config.ts}` —
  простой битвы и опрос battle (`GetMany`).
- `src/infrastructure/grpc/clients/battle.client.ts` — gRPC-клиент battle (`init`, `getMany`).
- `.env.dist` — `WEBSOCKET_PORT=8080`, `SERVICE_{AUTH,PROFILE,ENGINE,BATTLE}_URL`,
  `IDDLE_WEBSOCKET_TIME_LIMIT`, `BATTLE_IDLE_TIMEOUT_SECONDS`, `BATTLE_POLL_INTERVAL_SECONDS`.
