# Dependency direction

- shared → shared (только перечисленные выше рёбра) и shared → `proto` (только codegen у errors).
- **shared никогда не импортирует сервисы** и не знает об их существовании.
- Сервисы → shared через `"@shared/<pkg>": "workspace:*"`; запрещено копирование кода
  shared в сервисы и обратное переиспользование кода сервисов в shared.
