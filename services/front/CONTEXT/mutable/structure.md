# Structure

```
services/front/
├── lib/
│   ├── main.dart, app.dart, router.dart, theme.dart
│   ├── features/        # battle, home, profile, setting, update (экраны + состояние)
│   ├── domain/          # battle, profile, setting (модели клиента)
│   ├── infrastructure/  # auth, http, websocket, storage, device, proto, localization, error, app
│   ├── contracts/proto/ # сгенерированный Dart из ../../proto (не вручную)
│   └── shared/
├── android/, ios/, web/, linux/, macos/, windows/   # платформенные обёртки
├── assets/, test/, pubspec.yaml, analysis_options.yaml
└── firebase.json        # Crashlytics/Analytics
```
