# Architectural rules

- **Git-submodules**: `services/{auth,battle,bot,engine,front,gateway,orchestration,profile,streaming}`,
  `proto/`, `context/` и `shared/{logger,errors,kafka-manager,grpc-client-manager,
  pg-boss-manager}` — отдельные репозитории; правки в них коммитятся в свои
  репозитории (`make git-commit-all`). `k8s/` — часть infra-репо.
- **Shared-пакеты**: репозитории `@shared/*` общие с lenvora (`beartrando/logger`,
  `errors`, `kafka-manager`, `grpc-client-manager`, `pg-boss-manager`), в монорепе
  подключены сабмодулями и резолвятся только через корневые `overrides`
  (`workspace:*`); правка в shared-пакете задевает оба проекта.
- **Топология dev-запуска** (сеть `backend`): наружу публикуются gateway `9090`,
  streaming `8080`, kafka `9092`, postgres `5432`; остальное — только внутри сети.
  Доступ к БД — через `pgbouncer` (6432), Redis используют engine и orchestration.
- **Пересборка Docker-образов** (`docker compose build`) не выполняется без явного
  указания пользователя: dev-контейнеры получают код через volume-монтирования
  (`src`, `package.json`, `shared`) и пересоздаются через `docker compose up -d`.
- **Деплой**: активен GKE (`deploy-gke.yml`, `kubectl` + kustomize поверх `k8s/`);
  после истечения триала — docker-compose по SSH. `deploy-ssh-k8s.yml` — только
  `workflow_dispatch` (ручной/legacy), `deploy-gke.yml` и `deploy-docker-compose.yml`
  живут на `repository_dispatch`. `README.md` описывает план K3s — это план, не факт.
- **Codegen**: proto генерируется отдельно для каждого пакета (`make proto-generate`,
  см. [../proto/](../proto/CONTEXT/purpose.md)), не централизованно.
- **Контекст**: дерево `CONTEXT/` живёт в репозитории `context/` и колокейтится в проект
  только вручную: `make context-init` (однократная раскладка), `make context-capture`
  (обратная копия), `make context-drop` (удаление колокейтед-копий). Автосинхронизации нет.
