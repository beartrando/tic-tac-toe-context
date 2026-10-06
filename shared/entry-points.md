# Entry points

Нет — это библиотеки. Импорт: `import logger from "@shared/logger"` и т. п.
Точки сборки: `pnpm --filter @shared/<pkg> build`; в turbo-графе — `dependsOn: ["^build"]`.
