# Non-responsibility

- Решением, **когда** ошибка возникает (бизнес-проверки — в сервисах).
- Транспортом ошибок по сети — это gRPC-хендлеры сервисов и `ErrorObject` из proto.
- Определением самого `ErrorCode` (→ [../../../proto/](../../../proto/CONTEXT/purpose.md)).
