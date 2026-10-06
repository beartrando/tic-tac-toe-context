# Structure

- `src/infrastructure/http/` — `routes/health.routes.ts`,
  `controllers/{gateway,proxy}-controller.ts`, `middlewares/` (request-logger — фактически
  не подключён), `configs/`.
- `src/infrastructure/grpc/clients/health.aggregator.ts` — фан-аут `Health.Check`;
  `clients/orchestration.client.ts` — прокси-вызов.
- `src/infrastructure/grpc/config/microservices.config.ts` — карта `SERVICE_*_URL`.
- `src/application/use-cases/health/` — `health`, `full-health`, `status`, `livez`,
  `readyz`.
