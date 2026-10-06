# Architectural rules

- Единый топик `bot.connecting-request`: и producer (streaming), и consumer (bot) обязаны
  использовать одно и то же env-значение — менять синхронно в обоих `.env.dist`.
- Дефолт `HTTP_GATEWAY` в коде — `gateway:8080`, но фактический порт gateway `9090`;
  нормировано только env — не полагаться на дефолт.
- Логика «как играть» живёт в use-cases/state-store бота и не дублируется в engine.
