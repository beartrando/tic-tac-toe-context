# Architectural rules

- Ответ прокси возвращается как JSON поверх HTTP; конвертация
  `ProxyResponse → HTTP` — в proxy-controller/adapter оркестратора.
- Health-эндпоинты не проксируются — они локальные, в остальное — строго в
  `Orchestration.Proxy`.
- `SERVICE_*_URL` в `.env.dist` — это адреса health-фан-аута, не проксируемые маршруты.
