# Children

- [../auth/](../auth/CONTEXT/purpose.md) — идентичность: анонимный вход, сессии, токены; gRPC Auth.
- [../profile/](../profile/CONTEXT/purpose.md) — профили игроков; gRPC Profile, Kafka user.created → profile.created.
- [../gateway/](../gateway/CONTEXT/purpose.md) — единственная HTTP-точка входа снаружи: health + прокси в оркестратор.
- [../orchestration/](../orchestration/CONTEXT/purpose.md) — внутренний маршрутизатор HTTP-запросов поверх gRPC (Proxy).
- [../battle/](../battle/CONTEXT/purpose.md) — реестр битв: создание/подключение/выход; gRPC Battle, матчинг в Kafka.
- [../engine/](../engine/CONTEXT/purpose.md) — игровая логика: ходы, таймеры, состояние; gRPC Engine.BattleChannel.
- [../streaming/](../streaming/CONTEXT/purpose.md) — WebSocket-сервер для клиентов + bidi-канал в engine.
- [../bot/](../bot/CONTEXT/purpose.md) — AI-соперник: подписка на битвы, игра через gateway/streaming.
- [../front/](../front/CONTEXT/purpose.md) — Flutter-клиент (mobile/desktop/web), отдельная архитектура.

Без CONTEXT/ (не являются сервисами, prune в `make/context.mk`): `postgres/`,
`pgbouncer/` — конфиги и данные для docker-compose (`pgbouncer.ini`, `userlist.txt`,
каталог данных БД).
