# Architectural rules

- Новый класс ошибки = новый `ErrorCode` в `common/error.proto` → `proto:generate`
  здесь → файл в `src/errors/` → экспорт из `src/index.ts` → маппинг в хендлерах сервисов
  (одна волна, см. [shared/](../../CONTEXT/purpose.md)).
- `src/contracts/proto/**` не редактируется руками.
