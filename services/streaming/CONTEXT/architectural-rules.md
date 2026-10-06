# Architectural rules

- Любое WS-действие клиента, влияющее на битву, идёт строго по цепочке
  WS → `grpc-stream/engine.stream` → engine; напрямую в Kafka битвы не трогаются.
- `streaming.proto` не определяет `service` — это сообщения для потока; не искать
  генерируемый Streaming-сервер.
- `handlers/battle.service.ts` и `handlers/profile.service.ts` — закомментированный
  мёртвый код (ссылается на несуществующие `../grpc/generated/*`); не брать за образец.
