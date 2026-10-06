# Non-responsibility

- Вся серверная логика и хранение данных (→ [services/](../../CONTEXT/purpose.md) и
  дочерние сервисы).
- Определения контрактов (→ [../../../proto/](../../../proto/CONTEXT/purpose.md)) — front только
  генерирует из них Dart-код.
- Общие npm-библиотеки `@shared/*` — они не потребляются Dart-кодом.
