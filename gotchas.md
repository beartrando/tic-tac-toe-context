# Грабли и обнаруженные особенности

## Субмодули

- `docs/context`, `proto`, `make`, `docs/workflow` — **standalone-репозитории** (собственный
  `.git`-каталог), а не классические субмодули (gitfile). `git submodule status` помечает их
  как неинициализированные (`-`), хотя файлы на месте. `docs/wiki` — классический субмодуль
  (gitfile → `.git/modules/docs/wiki`).
- Расхождение регистрации `docs/context`: в `.gitmodules` — `submodule.context`,
  url `git@github.com:beartrando/tic-tac-toe-context.git`; в `.git/config` —
  `submodule.docs/context`, url `git@github.com:beartrando/context.git`.
  `git submodule update --init docs/context` отработает некорректно.
- `services/postgres` и `services/pgbouncer` — просто конфиги в монорепо, не сервисы и не субмодули.

## Makefile

- `make install`: `git submodule update --init --recursive` **закомментирован** — субмодули
  нужно инициализировать вручную.
- `make install`: цикл `for s in $(FRONT_SERVICES)` — переменная не определена
  (есть только `FLUTTER_SERVICES`), поэтому `env-generate` для front не выполняется.
- `make install-deploy`: путь `$(SERVICE_DIR)/.env.$$service` не содержит имя сервиса
  (вероятно, должно быть `$(SERVICE_DIR)/$$service/.env`).
- `make migrate` внутри контейнера: `docker compose exec -w ... npx prisma migrate dev` —
  интерактивная команда, в CI не подходит (для прода — `prod-migrate`).

## Процесс

- Свежий клон: `docs/context` пуст — сначала восстанавливается/инициализируется вручную.
- `make reset` останавливает и удаляет **все** контейнеры и volumes на машине, не только проекта.
- turbo-кэш и логи сборки лежат в `services/*/.turbo/`.
- Сгенерированные `src/contracts/proto` и prisma-клиент не закоммичены. Перед сборкой
  сервиса их нужно сгенерировать (`make proto-generate`, `make prisma-generate`), иначе
  `pnpm --filter <svc> build` падает на отсутствующих типах (напр. `TracedBattle`, `PrismaClient`).
- `shared/elastic` — обычный workspace-пакет (не субмодуль), создан локально; отдельный
  репозиторий/субмодуль `beartrando/elastic` пока не заведён.
