# Responsibility

- Pnpm workspace (`pnpm-workspace.yaml`: пакеты `services/*` и `shared/*`) и turbo-таски
  (`turbo.json`: `build`, `dev`).
- Общая TypeScript-база (`tsconfig.base.json`); `@shared/*` резолвится через workspace-линки
  `workspace:*`.
- Локальная разработка: `Makefile` + фрагменты `make/*.mk` (`make/parameters.mk` — списки
  сервисов: `NODE_SERVICES`, `PRISMA_SERVICES`, `FLUTTER_SERVICES`, `GIT_SERVICES`),
  `docker-compose.yml` (рабочая копия, генерируется из `docker-compose.yml.dist`).
- Развёртывание: **сейчас активен GKE** (`.github/workflows/deploy-gke.yml` + манифесты
  `k8s/` под kustomize base/overlays); по истечении триала GKE (~неделя) деплой переходит
  на docker-compose по SSH (`repository_dispatch: deploy-ssh-docker-compose` →
  `deploy-docker-compose.yml`). Образы собираются в GHCR (`build-and-push.yml`, теги —
  `.env.dist`).
- Git-структура: сервисы, `proto/` и `context/` — git-submodules (`.gitmodules`);
  `shared/` — обычная часть infra-репозитория.
- Контекст: `context/` — отдельная репозитория контекста; колокейтед-копии `CONTEXT/`
  создаются и синхронизируются вручную (`make context-init` / `make context-capture`,
  см. `make/context.mk`).
