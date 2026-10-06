# Entry points

- `make install` — submodule'ы, env-файлы, codegen proto, `make up` (полная инициализация).
- `make up` / `docker compose up -d` — локальный запуск всех сервисов.
- `make migrate` / `make migrate service=<svc>` — Prisma-миграции в контейнерах.
- `make proto-generate` — перегенерация контрактов во всех пакетах.
- `make test` — тесты node-сервисов в контейнерах.
- `make healthloop` — циклическая проверка `/gateway/health`.
- `make git-commit-and-push-all` / `make git-pull-all` — мультирепо-команды (см. git-workflow).
- Деплой: `repository_dispatch` (`deploy-gke` → GKE; `deploy-ssh-docker-compose` → compose
  по SSH), сборка образов — `repository_dispatch` (`build-and-push`).
