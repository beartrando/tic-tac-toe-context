# Восстановление контекста — чек-лист

Порядок восстановления полного контекста проекта перед началом работы.
Историю гита (`git log`, `git show`, ...) читать не нужно — только текущее состояние
(`git status`, `git diff`, `git ls-files`).

## Шаги

1. **Корень монорепо**: `README.md` (ссылается на docs), `package.json`, `pnpm-workspace.yaml`,
   `turbo.json`, `Makefile`, `make/main.mk`, `make/git.mk`.
2. **Документация процесса**: `docs/workflow/context.md`, `docs/wiki/notions.md`.
3. **Этот репозиторий** (`docs/context`): все файлы — architecture, build-and-deploy,
   workflow-and-git, gotchas.
4. **Инфраструктура**: `docker-compose.yml` (+ `docker-compose.yml.dist`, `.deploy.dist`),
   `.env.dist`, `.gitmodules`, `k8s/`, `.github/workflows/`.
5. **Код**: `services/matchmaking` и `services/gateway` как reference-реализации;
   `shared/*` — общие библиотеки; `proto/` — контракты.
6. **Текущее состояние**: `git status --short --branch`, `git submodule status`.

## Стоп-краны (что может сбить с толку)

- Часть субмодулей может быть не инициализирована на свежем клоне; `docs/context` при этом пуст.
- Смешанная регистрация субмодулей (standalone-репозитории vs классические gitfile) — см. `gotchas.md`.
- `.env` файлы сервисов создаются из `.env.dist` только через `make install`.
- Если контекст неполон или противоречив — задать вопрос оператору (этап 0.0 workflow).
- AI-агент не выполняет git-операции (commit/push/branch/merge/checkout) — это зона сервиса
  `devorc`; агент только читает состояние. См. `workflow-and-git.md`.

## Быстрая проверка готовности

- `make git-check-dev` — все репо на ветке `dev` и с чистым worktree.
- `make healthloop` — все сервисы живы (health через gateway :9090).
