# Architectural rules

- `lib/contracts/proto/` не редактируется руками — только `make proto-generate`
  (protoc, `--proto_path=./proto`).
- Формат веток/PR — общий для всех репозиториев (см.
  [git-workflow.md](../../../CONTEXT/git-workflow.md)); базовая ветка
  `dev`.
- Секреты/окружение — через `flutter_dotenv`; не хардкодить адреса сервисов.
- Сборка/публикация приложения не входит в compose-топологию: `FLUTTER_SERVICES` в
  `make/parameters.mk` используется только для codegen и env-инициализации.
