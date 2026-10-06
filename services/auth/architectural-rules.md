# Architectural rules

- Миграции и сиды — только здесь: `make migrate service=auth`, seed через
  `src/infrastructure/db/seed`.
- Топик `user.created` — контракт с profile: переименование/изменение схемы сообщения
  выполняется согласованно с [../../profile/](../../profile/CONTEXT/purpose.md).
- Health `Check/Status/Livez/Readyz` регистрируется по шаблону общего `Health`-сервиса
  (`proto/common/health.proto`), без собственных отклонений.
