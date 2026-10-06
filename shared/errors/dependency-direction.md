# Dependency direction

Лист дерева: не зависит от других shared-пакетов. Потребители — все сервисы
(маппинг в gRPC-хендлерах: `DomainError` → `ErrorObject`).
