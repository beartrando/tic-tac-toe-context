# Architectural rules

- Фактический рассинхрон топиков: engine produce `engine.battle_finished`
  (`MESSAGE_PRODUCER_TOPIC_BATTLE_TERMINATED`), а battle consume
  `engine.battle_terminated` — до выравнивания значений консьюмер engine-событий
  не получает терминации. Любая правка топиков — согласованно в обоих `.env.dist`.
- Миграции `Battle`-модели — только здесь (`make migrate service=battle`).
- **`TimeOut` по таймауту**: если статус не менялся `BATTLE_STATUS_TIMEOUT_SECONDS` (180 с)
  и он не финальный (`Finished`/`Cancelled`/`TimeOut`), битва переводится в `TimeOut` и
  публикуется `battle.timeout`. Основание — `updated_at` (бампается **любым** save, в т.ч.
  join/init), т.е. активность = любой save; решение зафиксировано в тикете #59.
- Sweep идёт каждые `BATTLE_TIMEOUT_SWEEP_SECONDS` (10 с): `Application` стартует
  engine-ский `TimerService` под фиксированным ключом, колбэк прогоняет sweep и
  перевооружает таймер — так периодичность переживает рестарты сервиса.
- Изменение enum `BattleStatus` (`TimeOut` добавлен в domain/prisma/proto) требует
  согласованной правки трёх мапперов (`db/mappers/battle/status.mapper.ts`,
  `grpc/mappers/battle/battle-status.proto.mapper.ts`, `proto/battle.proto`).
- **Ожидаемо**: первый sweep после простоя (деплой/выклченный сервис) переведёт в `TimeOut`
  все исторические не-финальные битвы → массовый переход и пачка `battle.timeout`.
- Контракт `GetMany(BattleIdsRequest) → BattlesResponse` добавлен для опроса со стороны
  streaming (тикет #59). Существующий `Get` в этом же тикете исправлен: он собирал
  `BattleResponse` при контракте `BattlesResponse` (клиент получал `{}`), теперь
  отвечает `BattlesResponse{battles.items:[…]}`.
- Тесты — **колокейтед**: `src/**\/*.test.ts` рядом с кодом, `jest.config.js`
  (`testMatch: **\/src\/*.test.ts`). `tsconfig.json` исключает `*.test.ts` из сборки
  (иначе `tsc -p tsconfig.json` тянет тесты в `dist`), типизация тестов — через
  `tsconfig.test.json`; в Dockerfile тесты копируются вместе с `src/`.
