# Dependency direction

Лист дерева: зависит от `logger` и `kafka-manager` (KafkaBridge → createProducer).
Потребители — сервисы (через `job/`-слой infrastructure). Направление строго shared ← services.
