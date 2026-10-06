# Dependency direction

Лист дерева (внутри shared зависит только от `logger`). Потребители — infrastructure-слой
сервисов; направление строго shared ← services.
