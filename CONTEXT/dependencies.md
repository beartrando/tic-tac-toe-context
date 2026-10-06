# Dependencies

Корень не импортирует код приложений. Потребители корневых конфигов — дочерние пакеты
(наследуют `tsconfig.base.json`, читают таски turbo) и инструменты внешнего окружения:
pnpm, turbo, protoc, docker compose, kubectl/kustomize, GitHub Actions.
