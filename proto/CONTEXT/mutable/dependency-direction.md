# Dependency direction

Схема потребления:

- `services/*` → здесь **на этапе codegen**: `pnpm <svc> proto:generate`
  (protoc + ts-proto, `outputServices=grpc-js`) → `src/contracts/proto/`.
- `shared/errors` → здесь на codegen (только `common/error.proto`, `outputServices=none`).
- `services/front` → здесь на codegen (protoc `--dart_out`) в `lib/contracts/proto/`.
- Docker: каждый Dockerfile копирует `proto/` и запускает codegen на стадии `deps`.
- Обратных рантайм-зависимостей нет; правка `.proto` не ломает компиляцию, пока
  codegen не перезапущен.
