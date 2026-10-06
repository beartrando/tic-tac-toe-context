# Entry points

- HTTP `:9090` — единственная внешняя HTTP-точка платформы (публикуется в compose).
- gRPC `:50051` — только `Health` (для фан-аута со стороны инфраструктуры).
- `make healthloop` ходит на `http://127.0.0.1:9090/gateway/health`.
