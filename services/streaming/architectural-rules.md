# Architectural rules

- Любое WS-действие клиента, влияющее на битву, идёт строго по цепочке
  WS → `grpc-stream/engine.stream` → engine; напрямую в Kafka битвы не трогаются.
- `streaming.proto` не определяет `service` — это сообщения для потока; не искать
  генерируемый Streaming-сервер.
- `handlers/battle.service.ts` и `handlers/profile.service.ts` — закомментированный
  мёртвый код (ссылается на несуществующие `../grpc/generated/*`); не брать за образец.
- Узнать о завершении «залипшей» битвы стримминг может **только** опросом battle
  (`Battle/GetMany`): engine-канал в это время молчит, а `TimeOut` битве ставит battle.
  Опрос повторяется каждые 10 с, пока ответ не терминальный; пакеты (in/out) по битве
  сбрасывают простой и убирают битву из опроса. Механика — engine-ский `TimerService`
  (ключ — battleId), а не `setInterval`/отдельный трекер. Новые статусы добавляются
  согласованно в `domain/battle/enums` + proto-маппер (`isTerminated()`).
- Тесты — **колокейтед**: `src/**/*.spec.ts` рядом с кодом, `jest.config.js`
  (`testMatch: **/src/**/*.spec.ts`). `tsconfig.json` исключает `*.spec.ts` из сборки,
  типизация тестов — через `tsconfig.spec.json`; в Dockerfile тесты копируются вместе
  с `src/`.
