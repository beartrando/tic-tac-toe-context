# Important files

- `common/error.proto` — `ErrorCode`, на котором построен `@shared/errors`.
- `common/health.proto` — контракт health-эндпоинтов всех сервисов и gateway-агрегатора.
- `engine.proto` + `streaming.proto` — язык bidi-потока engine ↔ streaming (меняются парно).
- `orchestration.proto` — `Proxy`, через который идёт весь внешний HTTP.
