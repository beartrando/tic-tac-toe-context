# tic-tac-toe-context

Репозитория контекста проекта **tic-tac-toe** (infra-монорепо). Хранит дерево узлов
`CONTEXT/`, зеркалящее структуру проекта, — по одному каталогу `CONTEXT/` на каждый
значимый узел. В проекте это **не** лежит постоянно: узлы колокейтятся вручную
через цели `make` из infra-репозитория (`make/context.mk`).

## Структура

```
CONTEXT/                          # корень infra-монорепо (единственный узел с git-workflow.md)
services/CONTEXT/                 # уровень приложений (общий шаблон Node-сервиса, топология)
services/{auth,battle,bot,engine,front,gateway,orchestration,profile,streaming}/CONTEXT/
shared/CONTEXT/                   # уровень библиотек @shared/*
shared/{errors,grpc-client-manager,kafka-manager,logger,pg-boss-manager}/CONTEXT/
proto/CONTEXT/                    # контракты gRPC/protobuf (git-submodule)
```

Директории `services/postgres`, `services/pgbouncer` и `context/` CONTEXT не имеют
и исключены из `context-*` целей (`EXCLUDED_DIRS` в `make/context.mk`).

## Формат узла

Каждый узел — набор из 13 файлов (плюс `git-workflow.md` только в корне):

| Файл | Содержание |
|---|---|
| `purpose.md` | чем является узел (заголовок + абзац) |
| `responsibility.md` | за что отвечает |
| `non-responsibility.md` | за что НЕ отвечает (и куда смотреть вместо этого) |
| `structure.md` | внутренняя структура |
| `children.md` | дочерние узлы (ссылки) |
| `parent.md` | родительский узел (ссылка) |
| `dependencies.md` | от чего зависит (фактически) |
| `dependency-direction.md` | направление зависимостей, шаблоны |
| `entry-points.md` | как зайти/запустить |
| `important-files.md` | ключевые файлы |
| `navigation.md` | «задача такая → иди туда» |
| `architectural-rules.md` | правила и зафиксированные особенности |
| `abstraction-level.md` | какое место узел занимает в дереве |
| `git-workflow.md` | (только корень) ветвление и мультирепо-команды — флоу общий для всех репозиториев |

Ссылки — обычные относительные markdown-ссылки на `.../CONTEXT/purpose.md`.

## Синхронизация (только вручную)

Автосинхронизации нет. Цели выполняются из **infra-репозитория**:

1. `make context-init` — однократная раскладка `CONTEXT/` из этой репы в проект
   (запрещена, если колокейтед-копии уже есть).
2. Правки контекста делаются в колокейтед-копиях рядом с кодом.
3. `make context-capture` — обратная копия колокейтед-копий сюда, затем коммит/пуш
   в эту репу.
4. `make context-drop` — удалить все колокейтед-копии `CONTEXT/` из проекта
   (с подтверждением), например перед повторным `context-init`.

Базовая ветка — `dev`, формат веток/PR — как описано в
[CONTEXT/git-workflow.md](CONTEXT/git-workflow.md) (общий для всех репо проекта).
