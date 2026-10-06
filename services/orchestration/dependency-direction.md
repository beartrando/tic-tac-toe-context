# Dependency direction

Как в родителе. Специфика: `proxy/routes` → `controllers` → gRPC-клиенты — весь
внутренний граф обслуживает единственную точку входа `Orchestration.Proxy`;
домен как таковой отсутствует.
