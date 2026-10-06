# Architectural rules

- Каждый пакет собирается независимо (`dist/` в .gitignore — не коммитится);
  потребитель видит только `dist/index.js` + `index.d.ts`.
- Тестов и jest-конфигурации у shared-пакетов нет.
- Jest сервисов маппит `@shared/*` на **корень пакета**
  (`'^@shared/(.*)$': '<rootDir>/../../shared/$1'`) → резолв идёт в `dist`; после правки
  shared нужно пересобрать пакет, иначе тесты сервисов увидят старый код.
- `shared/errors` генерируется своим скриптом `proto:generate`
  (`outputServices=none`, только `common/error.proto`).
- `grpc-client-manager` покрывает **только unary-вызовы** (`call(fn)` с одним ретраем);
  stream/bidi-RPC им не обслуживаются — для таких каналов потребители создают отдельные
  grpc-js клиенты в своей инфраструктуре (см. streaming → engine.BattleChannel).
- Расширение `ErrorCode` (`proto/common/error.proto`) — волновой шаг: proto →
  codegen `@shared/errors` → новые классы → экспорт → маппинг в хендлерах сервисов;
  выполняется осознанно, одной волной.
