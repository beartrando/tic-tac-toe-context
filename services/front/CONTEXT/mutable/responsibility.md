# Responsibility

- Экраны и фичи: `lib/features/{battle,home,profile,setting,update}`.
- Домен клиента: `lib/domain/{battle,profile,setting}`.
- Транспорт: `lib/infrastructure/{auth,http,websocket,storage,device,proto,localization,error,app}` —
  токены (secure storage, jwt_decode), HTTP-клиент, WS-канал, device-id.
- Контракты: `lib/contracts/proto/` — **Dart-codegen** из общего `proto/`
  (`protoc --dart_out`, см. `make proto-generate`).
- Навигация и стиль: `lib/router.dart`, `lib/theme.dart`, `lib/app.dart`.
- Firebase: `firebase_core` + `firebase_crashlytics` + `firebase_analytics`
  инициализируются в `main.dart` (на поддерживаемых платформах), конфиг — `firebase.json`.
