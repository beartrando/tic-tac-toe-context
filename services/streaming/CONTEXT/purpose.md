# Streaming — реальное время для клиентов

WebSocket-сервер для [../../front/](../../front/CONTEXT/purpose.md) и мост между клиентскими
событиями битвы и [../../engine/](../../engine/CONTEXT/purpose.md): держит bidi-канал
`Engine.BattleChannel`, форвардит туда действия игроков, а состояние возвращает клиентам
по WS. Также инициирует подключение бота событием Kafka.
