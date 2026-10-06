# Dependency direction

Как в родителе. Специфика: токен-сервис подменён через порт
`application/ports/token-service.port.ts` → `infrastructure/auth/service.ts`;
Prisma-репозитории `implements` интерфейсы из `domain/{user,session,device}/`.
