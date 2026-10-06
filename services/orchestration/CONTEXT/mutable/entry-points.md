# Entry points

- gRPC `:50051` — `Orchestration` (Proxy) + `Health`; наружу не публикуется.
- Клиенты: исходящие gRPC `auth:50051`, `profile:50051`; Redis; pgbouncer (pg-boss).
