# Entry points

- gRPC `:50051` — `Profile` + `Health` (только внутренняя сеть).
- Входящее: Kafka `user.created`; исходящее: Kafka `profile.created`.
