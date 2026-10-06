# Dependencies

Нет. Каталог — пассивный набор файлов, читаемых `protoc` по пути `--proto_path`.
Внутри контрактов есть только proto-импорты: `profile.proto` → `auth.proto`,
`engine.proto`/`streaming.proto` → `battle.proto` + `common/*`.
