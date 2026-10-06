# Dependency direction

- front → [../../../proto/](../../../proto/CONTEXT/purpose.md) **только на codegen**
  (`protoc --dart_out=lib/contracts/proto`), не рантайм-зависимость.
- front → бэкенд исключительно по сети: HTTP `gateway:9090`, WS `streaming:8080`.
- Node-пакеты (`@shared/*`, workspace) front не используют; обратно сервисы не знают
  о front кроме как о WS/HTTP-клиенте.
