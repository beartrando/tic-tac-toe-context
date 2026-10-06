# Structure

- `src/infrastructure/http/` — `client.ts`, `configs/http.client.config.ts` (gateway).
- `src/infrastructure/websocket/` — `websocket.client.ts`, `client.service.ts`,
  `battle.client.ts` (фактически закомментирован — мёртвый код), `configs/`.
- `src/infrastructure/message/` — консьюмер `bot.connecting-request`.
- `src/infrastructure/state-store/` — состояние партии бота.
- `src/domain/{battle,profile,user,session,device}/`, `src/application/use-cases/`.
