# Dependencies

Внутренние (фактические):

```
logger            ← kafka-manager, grpc-client-manager, pg-boss-manager
kafka-manager     ← pg-boss-manager (KafkaBridge использует createProducer)
errors            ← (внутри shared никто; зависит от proto/common/error.proto только на codegen)
```

Внешние: `kafkajs`, `@grpc/grpc-js`, `pg-boss`, `@bufbuild/protobuf` (только errors).
