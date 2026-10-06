# Dependency direction

Как в родителе. Специфика: домен `domain/profile/` не знает о user-сущностях auth —
`user_id` приходит как строковый ключ из Kafka/гRPC.
