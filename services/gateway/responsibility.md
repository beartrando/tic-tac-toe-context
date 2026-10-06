# Responsibility

- HTTP-сервис Express (`HTTP_PORT ?? 9090`): health-роуты `/gateway/*`
  (`health`, `full-health`, `status`, `livez`, `readyz`), весь остальной трафик →
  `proxy-controller` → gRPC `orchestration:50051`.
- gRPC-сервер `Health` на `:50051`.
- `HealthAggregator` — health-фан-аут по всем сервисам из `SERVICE_*_URL`
  (bot, auth, profile, orchestration, engine, battle, streaming).
- Конвертация HTTP-запроса в `ProxyRequest` и вызов `Orchestration.Proxy`.
