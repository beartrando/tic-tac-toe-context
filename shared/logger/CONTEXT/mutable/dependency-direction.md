# Dependency direction

Лист дерева: зависит только от окружения Node. Потребители (внутри shared):
`kafka-manager`, `grpc-client-manager`, `pg-boss-manager`; далее — все сервисы.
