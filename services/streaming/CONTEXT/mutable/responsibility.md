# Responsibility

- WebSocket-сервер на `:8080` (`infrastructure/websocket/`): приём/аутентификация
  соединений, `connection.handler.ts`, `handlers/`, `registers/` (реестры WS по битвам),
  `senders/`; простой по `IDDLE_WEBSOCKET_TIME_LIMIT`.
- Bidi-клиент `Engine.BattleChannel` (`infrastructure/grpc-stream/channels/engine.stream.ts`,
  `service.ts`) — create/start/move/leave/ping в engine и возврат `BattleResponse`.
- gRPC-клиенты `auth`, `profile`, `engine`, `battle` по `SERVICE_*_URL`
  (`infrastructure/grpc/configs/grpc.config.ts`).
- Produce Kafka-события `bot.connecting-request` — просьба подключить
  [../../bot/](../../bot/CONTEXT/purpose.md) к битве.
- **Таймаут простоя битвы** (`infrastructure/battle-timeout/`): трекер активности
  (`battle-activity.tracker.ts`, входящие WS-пакеты + исходящие
  `BattleWebsocketSender.send`) и опрос battle (`BattleTimeoutPollService`): после
  `BATTLE_IDLE_TIMEOUT_SECONDS` (180) простоя по битве опрос идёт каждые
  `BATTLE_POLL_INTERVAL_SECONDS` (10) через gRPC `GetMany`; на терминальный статус —
  push `BattleResponse` клиентам → `unsubscribeBattle` → выход из опроса.
- Use-cases: `application/use-cases/{auth,battle,websocket,health}`; Prisma-датасource
  без моделей (под pg-boss); `state-store/`, `timer/`.
