# Context — рабочий контекст AI-агента

Долговременный рабочий контекст для проекта tic-tac-toe (монорепо `infra`).

## Как пользоваться

1. Начни с [context-restoration.md](context-restoration.md) — чек-лист восстановления контекста.
2. Дополняй контекст в процессе работы (архитектура, решения, зависимости, грабли) — по правилам
   `docs/workflow/context.md` изменения в context можно делать свободно, без согласования.

## Содержание

- `context-restoration.md` — пошаговый чек-лист восстановления контекста проекта
- `architecture.md` — сервисы, инфраструктура, взаимодействие, shared-модули, proto-контракты
- `build-and-deploy.md` — сборка, запуск, миграции, тесты, k8s, CI/CD
- `workflow-and-git.md` — workflow AI-агента, конвенции git, make-команды для git
- `gotchas.md` — обнаруженные особенности и баги, влияющие на работу

## Сопутствующие репозитории (субмодули)

- `docs/workflow` (beartrando/workflow) — процесс разработки, этапы 0.0–5
- `docs/wiki` (beartrando/tic-tac-toe-wiki) — соглашения и понятия
- `proto` (beartrando/tic-tac-toe-proto) — protobuf-контракты
- `make` (beartrando/make) — make-скрипты проекта
