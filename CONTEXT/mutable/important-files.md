# Important files

- `pnpm-workspace.yaml` — что является пакетом.
- `turbo.json` — таски сборки (`build`, `dev`).
- `Makefile`, `make/parameters.mk` — списки сервисов и команды разработчика.
- `make/context.mk` — цели `context-init` / `context-capture` / `context-drop`
  (колокейтинг контекста, prune `services/postgres`, `services/pgbouncer` и `context/`).
- `make/git.mk` — мультирепо-команды (`git-commit-all`, `git-push-all`, `git-pull-all`).
- `docker-compose.yml.dist`, `docker-compose.yml.deploy.dist` — топология запуска dev/deploy.
- `.gitmodules` — какие директории являются подпроектами (включая `context/`).
- `k8s/overlays/production/` — точка входа манифестов для текущего GKE-деплоя.
