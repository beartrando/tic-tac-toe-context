# Orchestration — внутренний маршрутизатор

Принимает весь проксируемый HTTP-трафик от [../../gateway/](../../gateway/CONTEXT/purpose.md)
(gRPC `Orchestration.Proxy`), маршрутизирует его по внутренним gRPC-сервисам
(сейчас — auth и profile), проверяет JWT и кеширует ответы в Redis.
