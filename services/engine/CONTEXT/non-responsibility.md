# Non-responsibility

- Персистентного учёта битв как реестра (Prisma-моделей нет; это
  [../../battle/](../../battle/CONTEXT/purpose.md)).
- Транспорта до клиента (WebSocket) — это [../../streaming/](../../streaming/CONTEXT/purpose.md).
- Авторизации (только проверка JWT по `ACCESS_SECRET` для входящих потоков).
- Определений потока (→ [../../../proto/](../../../proto/CONTEXT/purpose.md), `engine.proto`).
