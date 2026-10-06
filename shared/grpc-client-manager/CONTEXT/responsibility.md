# Responsibility

- `GrpcClientManager<TClient>`: фабрика клиента, `call(fn)` — до 2 попыток с
  `reconnect()` на восстановимых ошибках (`isRecoverableError`).
- Единая точка создания клиентов для сервисных инфраструктур (health-агрегатор gateway,
  клиенты orchestration/streaming и др.).
