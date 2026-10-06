# Engine — игровая логика

Исполняет битву по правилам «крестиков-ноликов»: создание, старт, ходы, победа/ничья,
таймеры хода и старта. Получает события от
[../../streaming/](../../streaming/CONTEXT/purpose.md) по bidi-потоку `Engine.BattleChannel`,
состояние держит в Redis/state-store.
