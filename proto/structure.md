# Structure

```
proto/
├── auth.proto           # service Auth: Logout, RefreshTokens, AnonymousSignIn, Get
├── profile.proto        # service Profile: GetByUser, Get, GetOrCreate (импортирует auth.proto)
├── battle.proto         # service Battle: Get, Init, Leave + BattleObject/BattleStatus-модели
├── engine.proto         # service Engine: BattleChannel (bidi stream); пакет engine
│                        # + BattleRequest/BattleResponse (oneof create/start/move/leave/disconnect/ping)
├── orchestration.proto  # service Orchestration: Proxy(ProxyRequest) → ProxyResponse
├── streaming.proto      # service отсутствует: только BattleRequest/BattleResponse/…
│                        # (oneof: init/start/move/ping/leave/connect_bot) — язык событий
│                        # фронта для потока в engine
└── common/
    ├── health.proto     # service Health: Check, Status, Livez, Readyz
    ├── error.proto      # ErrorObject + ErrorCode (база для @shared/errors)
    ├── response.proto   # SuccessResponse
    ├── empty.proto, key_value.proto, ping.proto
```
