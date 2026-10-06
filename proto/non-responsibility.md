# Non-responsibility

- Генерируемый код (живёт в потребителях: `services/*/src/contracts/proto/`,
  `shared/errors/src/contracts/proto/`, `services/front/lib/contracts/proto/`).
- Реализация RPC, транспорт, адреса и настройки — это [../../services/](../../services/CONTEXT/purpose.md).
- Это **не pnpm-пакет**: в `pnpm-workspace.yaml` не объявлен, `package.json` нет,
  рантайм-зависимости от него ни у кого нет.
