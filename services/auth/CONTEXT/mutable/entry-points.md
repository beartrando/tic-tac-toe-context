# Entry points

- gRPC `:50051` — `Auth` + `Health` (наружу не публикуется; доступен только в сети
  `backend`).
- Исходящее: Kafka-топик `user.created`.
