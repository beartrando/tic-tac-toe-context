# Entry points

`import pgBossManager from "@shared/pg-boss-manager"`; стартует в `Application` на стадии
`job` (до db/grpc) в каждом сервисе с `PG_BOSS_*`-env.
