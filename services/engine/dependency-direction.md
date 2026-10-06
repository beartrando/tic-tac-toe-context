# Dependency direction

Как в родителе. Специфика: `engine.handler` (infrastructure) → `domain/battle` напрямую;
таймеры и кеш — infrastructure-зависимости, которые дёргает battle-service по мере
продвижения игры. Исходящий bidi-потоков нет — ответы пишутся в тот же канал
(`call.write`).
