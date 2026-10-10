# Сборка, запуск, деплой

## Makefile (корень, включает make/*.mk)

| target | что делает |
|---|---|
| `up` | `docker compose up -d` |
| `install` | локальная инициализация: submodule fetch, `.env` из `.env.dist`, docker-compose из `.dist`, proto-generate, reset |
| `install-deploy` | то же, но `submodule update --init --recursive` и compose из `.deploy.dist` |
| `reset` | stop/rm всех контейнеров, rm volumes, up, migrate, повторный up |
| `migrate [service=]` | `prisma migrate dev` для PRISMA_SERVICES |
| `prod-migrate` | миграции через docker-образы predeploy |
| `prisma-generate` | генерация prisma-клиентов |
| `seed` | `ts-node src/seed/seed.ts` для PRISMA_SERVICES |
| `proto-generate` | ts-proto для @shared/errors и всех Node-сервисов; dart для front |
| `test` | `pnpm test` внутри контейнеров всех Node-сервисов |
| `build` | `docker compose build` для всех Node-сервисов |
| `healthloop` | опрос http://127.0.0.1:9090/gateway/health каждые 5с |
| `reset-db` / `reset-kafka` | сброс volume + миграции |
| `artifacts-drop` | удаление node_modules, dist, tsconfig.tsbuildinfo |
| `fix-eof` | нормализация концов файлов (LF, один на конце) |

Git-команды (make/git.mk) — см. `workflow-and-git.md`.

## Окружение

- `services/<svc>/.env` — из `services/<svc>/.env.dist` (создаёт `make install`).
- Корневой `.env.dist` — версии образов (`GATEWAY_VERSION` и т.д.).
- `docker-compose.yml` генерируется из `docker-compose.yml.dist` (локально) /
  `docker-compose.yml.deploy.dist` (деплой).

## Kubernetes (`k8s/`)

- `base/` — namespace; `infrastructure/{kafka,kafka-ui,pgbouncer,postgres,redis}`;
  `services/{auth,battle,engine,orchestration,streaming,gateway,...}` (deployment, service,
  configmap, secret; ingress для gateway и streaming).
- `overlays/` — надстройки окружений.

## CI/CD (`.github/workflows/`)

`build-and-push`, `deploy-gke`, `deploy-ssh-k8s`, `deploy-docker-compose`,
`promote-dev-to-uat`, `promote-uat-to-prod`, `cleanup-registry`, `prune-docker-artifacts`.
Образы: `ghcr.io/temenb/<service>`, теги: `dev`/`uat`/`prod`, `predeploy` — для миграций.

## Проверки

- unit/интеграционные: `make test` (jest, `*.spec.ts`).
- typecheck/build: `pnpm --filter <svc> build` или `make build`.
- health: `make healthloop` (gateway агрегирует health всех сервисов).
