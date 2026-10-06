# Entry points

- WebSocket `:8080` — наружу публикуемый порт (клиенты фронта).
- gRPC `:50051` — `Health` (+ фактически неиспользуемые Battle/Profile-хендлеры).
- Kafka: produce `bot.connecting-request`.
