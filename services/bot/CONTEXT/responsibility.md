# Responsibility

- Consume Kafka `bot.connecting-request` — триггер подключения к конкретной битве.
- HTTP-клиент gateway (`infrastructure/http/`, env `HTTP_GATEWAY`) — регистрация/токены.
- WebSocket-клиент streaming (`infrastructure/websocket/`, env `WEBSOCKET_STREAMING`) —
  участие в битве, отправка ходов.
- Use-cases: `application/use-cases/{auth,profile,battle,health}`; `state-store/` —
  локальное состояние игры бота.
- gRPC `Health` на `:50051`; Prisma-датасource без моделей (под pg-boss).
- Тесты: `__tests__/functional/battle.test.ts`.
