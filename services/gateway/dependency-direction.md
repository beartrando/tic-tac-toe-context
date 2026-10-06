# Dependency direction

Как в родителе. Специфика: слоёв `domain/` и `application/ports/` не существует —
health-use-cases импортируют конкретный
`infrastructure/grpc/clients/health.aggregator`, proxy-controller — конкретный
`orchestration.client`.
