# Dependency direction

Как в родителе. Специфика: bidi-канал с engine созданный вручную (не через
`@shared/grpc-client-manager` — он unary-only); `websocket/` ← → `grpc-stream/` связаны
через application-use-cases и реестры `registers/`.
